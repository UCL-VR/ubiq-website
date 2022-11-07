---
title: "Extending the server"
description: "Subclassing the Room type to implement new multicasting behaviour"
lead: "See how the servers new extensibility methods allow you to implement your own multicasting models for research and experimentation."
date: 2022-11-07T09:19:42+01:00
lastmod: 2022-11-07T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["Sebastian Friston"]
---

## Introduction

Ubiq sessions are made up of Peers connected in a *Peer Group*.

By default, peers join *Rooms* on the server. Each room is a peer group, and group members exchange all messages with all other members.

This is just one possibility however...

In this post we show how to extend the server without making upstream code changes, so we can implement different multicasting behaviours, and so build different peer groups, for research and experimentation.


## Peers and Peer Groups

A peer group is defined as the set of peers who exchange messages - i.e. when a message is sent by one peer, which other peers recieve it.

When using the server, all peers make a connection to one process, and this process forwards messages between the connections. How these messages are forwarded - which peers they are forward to - defines the peer group.

The most common case is that peers use a `RoomClient` to join a *Room*, and the server forwards all messages between all members of a room.

In our recent scalability paper (under review), we compared two alternative ways to build peer groups - both based on peer locations. 
One partitioned the world into a set of cells, and the other made a group for each Peer out of the *k*-nearest other Peers.


## Extending the server

The Ubiq server is delivered as plain Javascript, so it is straightforward (and encouraged!) to modify it directly.

For simplicitly however, the server has patterns with extensibility in mind...


### Rooms

When the server starts up in `app.js`, it creates a new `RoomServer` class and adds some listening sockets to it. 
When peers make connections to one of these sockets, a new connection is created and wrapped by a `RoomPeer` instance. 

`RoomPeer` parses the messages from the peer's Room Client and arranges for the Room Peer to join a room.

```
onConnection(wrapped){
	console.log("RoomServer: Client Connection from " + wrapped.endpoint().address + ":" + wrapped.endpoint().port);
	new RoomPeer(this, wrapped);
}
```

Rooms are represented by objects which contain lists of their members. Object instances are created on-demand. The class itself determines how messages should be forwarded between the members.

By default, the Room Server will create rooms of type `Room`. This type forwards all messages, to all members:

```
processMessage(source, message){
	this.peers.forEach(peer =>{
		if(peer != source){
			peer.send(message);
		}
	});
}
```

However, it's possible to tell `RoomServer` to use a different class.


### Subclassing Room

In this example, we will add a new room type that creates groups of k-nearest neighbours.

1. Create a new class in a new file, e.g. `knnroom.js`.

2. In this new file, import `Room` so we can subclass it. `Room` implements a number of methods that `RoomServer` expects, so it's easiest to subclass it, though you can always copy it and modify the copy instead.

```
	const { Room } = require("./rooms")
	class KnnRoom extends Room{
		constructor(server){
			super(server);
		}
	}
```


3. Implement the new multicasting behaviour. There are a few important methods you'll need to consider for this...

	a. `addPeer` is called when a peer joins the room. In our subclass we put the peer in a list as before, but this list is no longer used to forward messages directly. Instead each peer is given its own set of arrays to maintain.
	
		addPeer(peer){
			peer.kNearestPeers = [];
			peer.kNearestPeerTimeouts = {};
			peer.kObservers = [];

			this.peers.push(peer);
			peer.setRoom(this);
		}
	
	
	b. `broadcastPeerProperties` is called when a peer updates its *Peer Dictionary*. 
	
	This is how we can get information to our subclass without implementing a new `RoomClient` message. 
	We check for the existence of a particular key and parse it, before handing control back to the superclass. 
	
	
	
		broadcastPeerProperties(peer,keys,values){
			// Intercept the position member
			var i = keys.indexOf("x.scalability.position");
			if(i > -1){
				var position = values[i];
				peer.position = JSON.parse(position);
			}
			super.broadcastPeerProperties(peer, keys, values);
		}

	In the snippet above, peers send their positions in the new key, and these are stored as vectors on the `RoomPeer` objects.
	
	In Unity, the key is set with a dedicated Component:
	
		IEnumerator PositionUpdate()
		{
			while (true)
			{
				roomClient.Me["x.scalability.position"] = JsonUtility.ToJson(PlayerTransform.position);
				yield return new WaitForSeconds(0.1f);
			}
		}
	
	c. In the constructor, we add the configuration for our subclass. Here we also, start an interval timer so we can get the subclass to call an update method at regular intervals.
	
		class KnnRoom extends Room{
			constructor(server){
				super(server);
				this.k = 20;
				setInterval(this.updateNearest.bind(this), 200);
			}
		}
	
	In `KnnRoom`, we compute the set of k-nearest neightbours for each peer, updating their `kNearestPeers` arrays.
	
		async updateNearestPeers(me){ // me is the peer

			// Get a list of other peers closest to this peer

			var peerDistances = [];
			this.peers.forEach(peer => {
				if(peer != me){
					if(peer.position !== undefined && me.position !== undefined){
						peerDistances.push({
							peer: peer,
							distance: KnnRoom.distance(peer.position, me.position)
						})
					}
				}
			});

			peerDistances.sort((a,b) =>{
				return a.distance - b.distance;
			});

			var nearest = peerDistances.slice(0, this.k).map(d => d.peer);

			// intersect to find who we have to drop, and who we have to add 

			var added = nearest.filter(x => !me.kNearestPeers.includes(x));
			var removed = me.kNearestPeers.filter(x => !nearest.includes(x));

			added.forEach(peer =>{
				me.kNearestPeers.push(peer);
				me.sendPeerAdded(peer);
				peer.kObservers.push(me); // add me to observers of the other peer, so I get its messages even if // I am not in its k-nearest group..
			});

			removed.forEach(peer =>{
				me.kNearestPeers.splice(me.kNearestPeers.indexOf(peer),1);
				me.sendPeerRemoved(peer); 
				peer.kObservers.splice(peer.kObservers.indexOf(me), 1); // remove me from observer of the other peer, as I am no longer interested in it. If it needs my messages, it will be in my observers group.
			});

		}
	
	d. `processMessage` (finally!) is the most important method. This is called whenever a peer sends a message to the peer group - it is here that the multicasting takes place.
	
		processMessage(source, message){
			source.kObservers.forEach(other =>{
				other.send(message);
			})
		}
		
	In `KnnRoom`, the logic turns out to be very simple, as all the work to define the peer group takes place in the `updateNearestPeers` method.
	
	Note how in the snippet above, we are actually forwarding to the `kObservers` array. This is the array of peers for which the peer sending the message is one of the *k*-closest other peers. 
	The peer will be in the `kObservers` array of all the peers in its own `kNearestPeers` array.
	

4. Now we have the new class, we must tell the `RoomServer` to use it. Make sure the class is visible by importing it somewhere into the `app.js`,

	
	`const { KnnRoom } = require("./knnroom");`
	

5. Then, modify the server config to specify the name of the new type,
	
```
	{
		"roomserver":
		{
			"ports":
			{
				"tcp": 8009,
			},
			"roomType":"KnnRoom"
		}
	}
```
	

Now, when `RoomServer` needs to create a room, it will create a `KnnRoom` instead of a `Room`.

Any number of peers can join a single room, and `KnnRoom` will multicast within it based on the relative positions of the peers.



### Server Messages

In the example above, we used the peer dictionary to communicate with our subclass, but we can send messages directly too.

By default, messages addressed to the Room Server are parsed by `RoomPeer`, which is the counterpart of `RoomClient`.

If a message is addressed to the Room Server but does not match an existing message type however, it is forwarded to the room itself. 

This is done through the `processRoomMessage(message)` method, with *message* having the schema:

```
{
    id: "/ubiq.rooms.servermessage",
    type: "object",
    properties: {
        type: {"type": "string"},
        args: {"type": "string"}
    },
    required: ["type","args"]
}
```

This functionality makes it possible for additional components to address the subclass directly.

For example, the following snippet uses a new message type, `SetObserved`, to maintain a second list of peers.

```
class MyExtendedRoom extends Room{
	// Forwarded to this room when the Peer sends a message to the server with an unknown Type
	processRoomMessage(peer, message){
		switch(message.type){
			case "SetObserved":
				this.changeObserved(peer, message.args.rooms);
				break;
		}
	}
}
```

In Unity, the RoomClient includes a public method, `SendToServer`, which can be used to send arbitrary messages intended to be picked up this way.

```
public class MyRoomAgent : MonoBehaviour
{
	private struct SetObservedRequest
	{
		public List<string> rooms;
	}

	/// <summary>
	/// Observes the Rooms with the specified Guids. Stops observing any not in the list, and begins observing any new ones.
	/// </summary>
	void SetObserved(List<Guid> guids)
	{
		roomClient.SendToServer(
			new Messages.Message(
				"SetObserved", 
				new SetObservedRequest()
				{
					rooms = guids.Select(g => g.ToString()).ToList()
				})
			);
	}
}
```

## Conclusion

A cornerstone of Ubiq is its peer-to-peer messaging based on *groups*. Groups are the peers that receive messages sent by one of their members.

By changing how messages are forwarded, we change the groups, and possibly the behaviour of users or an application.

To experiment with different models for groups, you can subclass the type that is responsible for multicasting and implment your own models, without making any changes to the Ubiq code.

To get the complete code for the above examples, and check out how these features were used in some our own experiments, have a look at the [ubiq-scalability-2022](https://github.com/UCL-VR/ubiq/tree/ubiq-scalability-2022) branch. 
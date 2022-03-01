---
title: "Design Pattern"
description: 
lead: 
date: 2020-09-21T16:00:43+02:00
lastmod: 2020-09-21T16:00:43+02:00
draft: false
images: []
menu:
  docs:
    parent: "async-design-patterns"
weight: 350
toc: true
---

## Asynchronous Design Patterns in Unity

The Unity process manages the main thread, which begins before any user code is executed. Most Unity resources can only be accessed from the main thread; an exception will be thrown otherwise. There are still many possibilities for writing aysnchronous code however.

### Design Pattern

Delayed initialisation with callbacks. Mimics the do-then pattern in JS. Methods are called which take Actions. Those Actions are initialised by lambdas. The lambas execution thread depends on the called function.

{{< highlight go >}} 
void Start()
{
    factory.GetRtcConfiguration(config =>
    {
        pc = factory.CreatePeerConnection(config, this);
    });           
} 
{{< /highlight >}}

### Design Pattern

Message pumps with Update. Commonly used in the mid-level networking code, this pattern uses a list of actions to execute methods on the main thread.

{{< highlight go >}} 
class NetworkScene
{
    private List<Action> actions = new List<Action>();

    public void RegisterComponent(INetworkComponent component)
    {
        actions.Add(() => 
        {
            if (!objectProperties.ContainsKey(networkObject))
            {
                objectProperties.Add(networkObject, new ObjectProperties()
                {
                    identity = networkObject,
                    scene = this,
                });
            }
        });
    }

    private void Update()
    {
        ReceiveConnectionMessages();
        foreach (var action in actions)
        {
            action();
        }
        actions.Clear();
    }
}
{{< /highlight >}}

### Design Pattern

Commonly used in webrtc code for objects that take time to initialise because they are waiting on external resources. This pattern uses coroutines to effectively poll a resource, conditionally executing operations on the main thread.

{{< highlight go >}} 
    void Start()
    {
        factory.GetRtcConfiguration(config =>
        {
            pc = factory.CreatePeerConnection(config, this);
        });           
    }

    private IEnumerator WaitForPeerConnection(Action OnPcCreated)
    {
        while (pc == null)
        {
            yield return null;
        }
        OnPcCreated();
    }

    public void AddLocalAudioSource()
    {
        StartCoroutine(WaitForPeerConnection(() =>
        {
            var audiosource = factory.CreateAudioSource();
            var audiotrack = factory.CreateAudioTrack("localAudioSource", audiosource);
            pc.AddTrack(audiotrack, new[] { "localAudioSource" });
        }));
    }
{{< /highlight >}}
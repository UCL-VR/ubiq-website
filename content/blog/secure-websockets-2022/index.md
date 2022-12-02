---
title: "Serving Secure WebSockets"
description: "How to set up the Ubiq server to allow connections from regular browsers"
lead: "The latest version of Ubiq serves secure WebSockets by default. See what you need to do to support this in your own deployments."
date: 2022-12-02T09:19:42+01:00
lastmod: 2022-12-02T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["Sebastian Friston"]
---

## Introduction

Ubiq 0.0.3 supports building for WebGL *out-of-the-box* in Unity.

Ubiq has always supported Javascript clients, but now users have the option of building native browser clients, or building for the web directly in Unity.

WebGL support is experimental, and some features are still a work in progress, such as Voice Chat.

This post is not about building for the browser however, but what it required to support the browser behind the scenes...



## Secure WebSockets

Unity can build applications for browser by targetting WebGL. 

This target transpiles the Unity game into WebAssembly, which runs within a tab's Javascript Runtime/Virtual Machine, with all its abilities and constraints.

There are three ways Javascript applications talk to the outside world. 

1. They can make regular HTTP calls using XHR. 
2. They can use the WebRtc API to create a Peer Connection, that can include a Data Channel operating similarly to UDP. 
3. Or they can use [WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API). These start out as HTTP connections which upgrade themselves into WebSocket connections, keeping the connection open and presenting something akin to a message-based version of TCP.

Opening WebSockets is straightforward in Javascript, but with restrictions on their use.

For example, the [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) [`upgrade-insecure-requests`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) prevents scripts upgrading an HTTPS connection to an insecure WebSocket connection, and the phase-out of insecure HTTP has been in progress for [years](https://blog.mozilla.org/security/2015/04/30/deprecating-non-secure-http/)...

While it is possible to [override](https://www.damirscorner.com/blog/posts/20210528-AllowingInsecureWebsocketConnections.html) this behavour it weakens security, and will only become harder as time goes on.

The latest version of the Ubiq tries to make doing the correct thing easy, by making secure WebSockets the default.

This requires some additional work to deploy however, to allow HTTPS to meet its goals.

**(And remember you don't need to do any of this if you don't want to recieve connections from the browser! You can also use our development server, Nexus.)**


## What is HTTPS?

The purpose of HTTPS is to provide *privacy* and *authentication*.

HTTPS is HTTP over TLS - Transport Security Layer. That is, it operates over a regular TCP connection but with encrypted traffic. 

When a TSL connection is opened, the client and server use an asymmetric cipher to agree on a way to encrypt data flowing between them.

This works by having the server offer up a public key. The client uses this to send a secret to the server. The nature of [public key encryption](https://en.wikipedia.org/wiki/Public-key_cryptography) means only the server (with its private key) can decode the secret. The secret is used to set up encryption for user data.

This provides privacy. But it's the provision of the public key that interests us however, because this is what enables the second goal of HTTPS: *authentication*.


### Certificates

Any public-private key pair could be used to create an encrypted connection between two sockets.

Browsers won't just accept any public key however, because doing so still leaves users vulnerable to [MITM attacks](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

It is not much use securing information from snooping, if it will be streamed directly to an attacker!

To protect against this, servers provide their public keys in a *certificate*. The certificate verifies that the public key belongs to the host, and that the host is who it claims to be.

Certificates themselves are signed with the private key of a Certificate Authority (CA). The Certificate Authority is an entity that can be trusted to issue certificates. 

Certificates have a *chain of trust*. Certificates can be used to create (sign) other certificates. At the top of the chain, is the Root Certificate, which is self-signed.

![me](chain-of-trust.png)
(CC BY-SA 4.0 Attribution: Yuhkih)

Root Certificates, which can be used to verify the entire chain, are distributed as part of operating systems and browsers.

Certificate Authorities are trusted by these [manufacturers](https://learn.microsoft.com/en-us/security/trusted-root/program-requirements), due to the significant [real world physical security](https://arstechnica.com/information-technology/2012/11/inside-symantecs-ssl-certificate-vault/) they employ to protect their private keys.


## What this means for custom deployments

Browsers won't make secure connections to a host unless it can prove its identity with a certifcate derived from one of the world's [Root CAs](https://w3techs.com/technologies/history_overview/ssl_certificate).

Therefore, the first step in deploying a Ubiq server supporting WebSockets is to acquire a certificate.

If you are using institutional resources, you can ask your technical support group for one.

Alternatively, you can purchase a certificate from a retail issuer such as the Comodo SSL Store.

The non-profit [Lets Encrypt](https://letsencrypt.org/) provides certificates free of charge.

If you choose to acquire your own certificate from Lets Encrypt or another provider, they will verify you own the domain the server is running on by asking you to fulfil various challenges. For example, updating a DNS record, or serving particular content at a specified URI. Once they have verified you control the domain, the certificate will be issued.

Once you have the certificate, it must be provided to the Ubiq Server.

This is done by setting the cert and key properties under the wss configuration to the paths of your certificate and private key.

```
{
    "roomserver":
    {
        "tcp":
        {
            "port": 8009
        },
        "wss":
        {
            "port": 8010,
            "cert": "./cert.pem",
            "key": "./key.pem"
        }
    },
    "iceservers":
    [
        {
            "uri" : "stun:stun.l.google.com:19302"
        }
    ]
}
```

The example configuration looks for the certificate and key in the same folder as `app.js`. 

The server expects the certificate and key to be in the PEM format. 

PEM is a container format which describes how a certificate or key should be stored (in base64) and the headers that describe what is in the PEM.

Different providers will deliver keys with various formats and extensions. Before doing anything else you should check if the certificate and key are already in PEM format by opening them with a text editor.

If the file starts with `-----BEGIN`, then the file is already in PEM format. If the content is binary, it will need to be converted to PEM.

For example, this is an (abridged) private key in PEM format generated with the command `ssh-keygen -f "C:\mykey.pem"`:


```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEAvLW7is3EEW52qjPalsDYTsIRdRut2MZfeKC0oL/idw+ZaX8+wJJz
kW+Es9Ddf0YAkuZQHyRK4GV5Z8D7Vl2yVJP0x6fgHZ5AOv5xi17KMrsCsN+2LJriwMFLCz
s7621XlIEIIrv+DqLDtR96bm9GRNWYola4c1jdHK3maLiVC3U0QeXIlFs7oJfnYPJrtO2Y
xlpKkQlng8f8ZgLDws8dGqnEwst+1I1Mg2wvfYsJ2jl9Ypg6DSXDw5Skl8KKrnUVNVvuYt
...
6aXtX7hgiq1uVekivPX6scILHXeOewfLRYUlv9WFDD5Veu2r0zzGeXkLCDUjJAnYAHe2uL
XkG1JGuymV/GOd6odbri0w5KvekggHTrZvLpZ/feLPTG3SdkExYgpayiX4OQGW0jXfiUmR
6K7eEpuj1UFqf5pm6fYrGVx5Ft9ral3fhg0Mn0s7P1t59HH4lP+lB+z7I1akiFge9s364T
wZPJTacvDtCD/3RQAAABlTZWJhc3RpYW5AREVTS1RPUC1GMUowTVJS
-----END OPENSSH PRIVATE KEY-----
```

You can also use `local.json` to override these on specific machines.

For example, on our server Nexus, our Technical Support Group writes certificates to `/root/cert`, so all Ubiq Server instances have a `local.json` configuration file that point to these:


```
{
    "roomserver":
    {
        "wss":
        {
            "cert": "/root/nexus.crt",
            "key": "./root/nexus.key"
        }
    }
}
```

Note that even though the naming convention & file extensions are different, the key is in the PEM format.

Once the certificate and key have been provided, the `WrappedSecureWebSocketServer` class uses them to serve a secure websocket server using an instance of [`https`](https://nodejs.org/api/https.html).

```
const server = createServer({
	cert: fs.readFileSync(certPath),
	key: fs.readFileSync(keyPath)
},
(req, res) => {
	res.writeHead(200);
	res.end('Welcome to Ubiq! This is a Ubiq WebSocket Server. To use this endpoint, connect to it with a Ubiq Client.\n');
  }
);
const wss = new WebSockets.WebSocketServer({ server }); // Take care to use the WebSocketServer member, as the import of ws provides the WebSocket *client* type.
this.port = config.port;
server.listen(this.port);
```

You can try and connect to this with a regular browser, and see its HTTPS response:

[https://nexus.cs.ucl.ac.uk:8010](https://nexus.cs.ucl.ac.uk:8010)

(The server will respond to HTTP requests, until it is asked to upgrade the connection to a WebSocket)


## Adding to the Trust Store

Another possibility is to self-sign your own certificates. This way, you become your own Root CA.

The downside of this is that you'll need to add your certificate to the Root Certificate store of all instances of all platforms you want to connect from.

So, for example, each copy of Chrome that you want to connect will have to [add your certificate to it](https://www.techrepublic.com/article/how-to-add-a-trusted-certificate-authority-certificate-to-chrome-and-firefox/) manually. You would not be able to run an in-the-wild-experiment like this, for example.

However it may be useful to run on a small set of machines internally, or for development.

You can [use OpenSSL](https://devcenter.heroku.com/articles/ssl-certificate-self) to generate certificates for these purposes.


## Insecure WebSockets

It is still possible to serve insecure WebSockets. To do this, modify `connections.js` and present the WebSocket using an instance of `http` rather than `https`.

The reason this is not supported as a configuration option is that there isn't really a use case for insecure WebSockets anymore! Browsers will not connect to them, and applications running natively should use TCP directly.


## Conclusion

Browsers are defaulting to ever more restrictive content policies. 

This is to make the web as safe as possible for regular users, but it does introduce complexity when offering web services.


If you want WebGL users to make connections to your own Ubiq server, the easiest way is to allow them to operate within their policies and make secure connections.

Making it easy to comply with these new requirements, and so ensuring the maximum number of administrators do, is an important part of making sure best practicies are adopted.

This is one of the motivations of Lets Encrypt, a non-profit providing free, automatic certificates to reduce the barriers to providing secure options.

For our part, the latest version of the Ubiq server offers Secure WebSockets by default. All users need to do is provide a certificate and private key for the machine they want to host it on.

And of course, if you don't want to provide WebSocket support, you don't have to do anything!

When starting the example server, it will detect if no certificate has been provided and simply not offer WebSockets. It will continue to function normally for all native (PC, Android, Quest, etc) peers out-of-the-box.

And as always, do reach out to the team via e-mail or [Github](https://github.com/UCL-VR/ubiq/discussions) with any questions!
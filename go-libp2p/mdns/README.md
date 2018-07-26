## libp2p peer discovery with mDNS

[go-libp2p](https://github.com/libp2p/go-libp2p) currently implements the [mDNS](https://tools.ietf.org/html/rfc6762)
for zero configuration peer discovery in local networks. mDNS solves the
problem of resolving and maintaining a mapping table between peerIDs and local
ports. With mDNS, it is possible for peers in local network to discover peers in
the local network without knowing of their existence previously and without the 
need to rely on [pre-configured bootstrapping schemes](https://github.com/ipfs/ipfs/issues/30).

The example of how libp2p hosts can use mDNS is in [/main.go](./main.go). The
setup is simple: 

1) Creates 2 libp2p hosts listening on all interfaces on `0.0.0.0` and in 
different ports;

2) Prints each of the host's Peerstores to show that the hosts are not aware of
the existence of each other;

3) Start a mDNS Service per host. The mDNS Service will multicast discovery 
requests at a certain interval of time and at the same time, listen to discovery
requests from other peers and reply to it. The mDNS response payload contains
the port where the peer is listening.

4) Define a `Notifee` for the mDNS Service of one of the hosts. The `Notifee`
consists of an object which implements a method
`HandlePeerFound(peerstore.PeerInfo)` which will handle the mDNS responses. The
example defines the method so that the host starts a connection with the
discovered peer. This way, both peers will add each other to their peerstores.

5) Prints each of the host's Peerstores to show that the hosts know about each
other and that the peer discovery was successful.

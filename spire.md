# SPIRE

> this is based on libp2p which is a peer-to-peer networking protocol designed to enable decentralized communication and file sharing over the internet. It is a modular, open-source networking protocol that allows nodes to communicate with each other directly, without the need for a central server or infrastructure.

## spire have config file which consists of different attr
### one of the is
> '"transport":{
>      "libp2p": {
>        "directPeersAddrs":[]}}

> this attr is reponsible for the making the peers, So we have to give the info like "/ip4/192.168.18.109/tcp/37705/p2p/12D3KooWPFpaE13gph8p6jdNGJv1M6fwDro8kdst53MUzVpuSJUL" i.e **"\<ip-version>/\<host>/\<protocol>/\<port>/\<type>/\<peer_id>"** w.r.t the quorum of median. 

### command to run spire
**spire agent -c /home/usman/docs/spire_feed2.json --log.verbosity debug**

## how it will work

> we should run the 3 feeds with the 3 spires 

> every feed should have their own omnia
> means the config file have the right omnia addr pasted in feed object of spire's config


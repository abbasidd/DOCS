# Feed
> So  You should have spire, you will get it from this repository: https://github.com/makerdao/oracle-suite.git. After that you have to configure it. the guide is in the mentioned file named spire.md and sample config file named spire_feed1.json.
> After that you have install omnia from this repository: https://github.com/chronicleprotocol/omnia.git

>you should have to export these env variable in your terminal 

export SPIRE_CONFIG=/home/usman/docs/spire_feed1.json
export OMNIA_CONFIG=/home/usman/docs/omnia_feed1.json
export GOFER_CONFIG=/home/usman/Videos/oracles/systemd/gofer.json
## command to run omnia
omnia 


# sample config 

 ``` json
 {
    "ethereum": {
      "from": "0xD260517CE442379231A7Fe62FD555D2E345F316f",
      "keystore": "/home/usman/.dapp/testnet/8545/keystore",
      "password": "/home/usman/.dapp/testnet/8545/keystore/pass"
    },
    "transport":{
      "libp2p": {
        "directPeersAddrs": [
            "/ip4/192.168.18.109/tcp/40969/p2p/12D3KooWKhhhiDqgCSfamK1stGUJuth8W4FFovDJhJDSNWFzXUPP",
            "/ip4/192.168.18.109/tcp/39577/p2p/12D3KooWDXWF9oMCcZYvrfua6tSTuaZvTvyCBedxwgtiJ5CiB9Fo"
  
        ]
      }
    },
    "feeds": [
  "0xD260517CE442379231A7Fe62FD555D2E345F316f",
  "0x70997970C51812dc3A010C7d01b50e0d17dc79C8",
  "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266"
    ],
    "spire": {
      "rpc": {
        "address": "127.0.0.1:8083"
      },
      "pairs": [
        "ETHUSD"
      ]
    }
  }
  
  ```
  
  
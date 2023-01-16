# **ORACLES**

## Architecture
Goals of this new architecture are:
<!-- 
1. Scalability,
2. Reduce costs by minimizing number of ethereum transactions and operations performed on-chain,
3. Increase reliability during periods of network congestion,
4. Reduce latency to react to price changes,
5. Make it easy to on-board price feeds for new collateral types, and
6. Make it easy to on-board new Oracles. -->


### Their Architecture
![plot](./old_architecture.png)
### Our Architecture
![plot](./new_architecture.png)
## Design Goals

#### The Oracle uses these services :
  
  1. Gofer
  2. Setzer
  3. Spire
  4. Omnia
  
  >Each of them are explained below

# GOFER 
>Gofer is a tool that provides reliable asset prices taken from various sources.

## Installation
>**##PREREQUISITE** It is recommended that you install Go version 1.18.1 on your system.:

    1 . git clone https://github.com/block360/oracle-suite.git

    2 . cd oracle-suite

    3 . export BUILD_DIR=`<Your bin path>` make; //like /usr/bin/ 

    **For running the the agent**:
    4 . gofer agent -c ./gofer.json --log.verbosity info
**NOTE** It will install both gofer and spire.
## Running the gofer 

> Gofer consists of two components that function like a client/server architecture: the gofer agent (server) and the gofer. The gofer agent should be running in order for the gofer to be able to pull prices from it.



    For getting the price from terminal:

    `gofer prices ETH/USD -c ./gofer.json --format trace`


## Sample config file gofer.json.



```json
{
  "ethereum": {
     "rpc": ["https://mainnet.infura.io/v3/de82b2d602264e4fbc0929dec0c45baa"]
  },
  "gofer": {
    "rpc": {
      "address": "127.0.0.1:8081"
    },
    "origins": {
    },
    "priceModels": {
       "ETH/GSU": {
        "method": "median",
        "sources": [
          [{ "origin": "gsu", "pair": "ETH/GSU" }]
        ],
        "params": {
          "minimumSuccessfulSources": 1
        }
      },
      "BTC/GSU": {
        "method": "median",
        "sources": [
          [{ "origin": "gsu", "pair": "BTC/GSU" }]
        ],
        "params": {
          "minimumSuccessfulSources": 1
        }
      },
      "BTC/USD": {
        "method": "median",
        "sources": [
          [{ "origin": "bitstamp", "pair": "BTC/USD" }],
          [{ "origin": "coinbasepro", "pair": "BTC/USD" }],
          [{ "origin": "kraken", "pair": "BTC/USD" }]
        ],
        "params": {
          "minimumSuccessfulSources": 3
        }
      },
      "ETH/USD": {
        "method": "median",
        "sources": [
          [{ "origin": "bitstamp", "pair": "ETH/USD" }],
          [{ "origin": "coinbasepro", "pair": "ETH/USD" }],
          [{ "origin": "kraken", "pair": "ETH/USD" }],
          [{ "origin": "uniswapV3", "pair": "ETH/USD" }]
        ],
        "params": {
          "minimumSuccessfulSources": 3
        }
      }
    }
  }
}
```

# **SETZER**
>**##PREREQUISITE** If you don't have make installed on your system then you can lookup to this article:https://linuxhint.com/install-make-ubuntu/

Dependencies:
these should be installed on your system.
* GNU [bc](https://www.gnu.org/software/bc/)
* [curl](https://curl.haxx.se/download.html)
* GNU [datamash](https://www.gnu.org/software/datamash/)
* GNU `date`
* [jshon](http://kmkeen.com/jshon/)
* GNU `timeout`

  # Installation

      1. git clone https://github.com/block360/setzer.git
      2. cd setzer
      3. make link
      4. make install

  Query USD price feeds
  

  ## Configuration

  * `SETZER_CACHE` - Cache directory (default: ~/.setzer)
  * `SETZER_CACHE_EXPIRY` - Cache expiry (default: 60) seconds
  * `SETZER_TIMEOUT` - HTTP request timeout (default: 10) seconds
  

  ## Usage

  ```
  Usage: setzer <command> [<args>]
    or: setzer <command> --help

  Commands:

    help            Print help about setzer or one of its subcommands
    pairs           List all supported pairs
    price           Show price(s) for a given asset or pair
    sources         Show price sources for a given asset or pair
    test            Test all price feeds
  ```

# **SPIRE**
>spire is installed throug oracle-suite project, so it is assumed to be installed on your system

This is based on libp2p which is a peer-to-peer networking protocol designed to enable decentralized communication and file sharing over the internet. It is a modular, open-source networking protocol that allows nodes to communicate with each other directly, without the need for a central server or infrastructure.

**spire have config file which consists of different attributes**
```json 
"transport":{
      "libp2p": {
        "directPeersAddrs":[]}}
  ```

 **this attribute is reponsible for the making the peers, So we have to give the info like `/ip4/192.168.18.109/tcp/37705/p2p/12D3KooWPFpaE13gph8p6jdNGJv1M6fwDro8kdst53MUzVpuSJUL` i.e **"\<ip-version>/\<host>/\<protocol>/\<port>/\<type>/\<peer_id>"** w.r.t the quorum of median.** 

### command to run spire
**spire agent -c /home/usman/docs/spire_feed2.json --log.verbosity debug**

### how it will work

 we should run the 3 feeds with the 3 spires 
 every feed should have their own omnia
 means the config file have the right omnia addr pasted in feed object of spire's config



# Omnia
>`git clone https://github.com/block360/omnia.git`

>`cd omnia` 
 ## installation

    Some convenience targets for `make` are available. If you have Docker installed, you can do
    
    ```
    make build          # build all the images
    make run            # run images after they're built
    make test           # build and run integration tests
    omnia # for running the omnia 
    ```
  
  `cat $(which omnia)` and open the parent dictory in last line command 
  You shuld have to change the setzer path in nix store where omnia is present
  file name that will be changed : exec/source-setzer and bin/omnia
  add `/usr/local/bin/setzer` in PATH enviroment variable.
  
> So  You should have spire, you will get it from this repository: https://github.com/makerdao/oracle-suite.git. After that you have to configure it. the guide is in the mentioned file named spire.md and sample config file named spire_feed1.json.

>**you should have to export these env variable in your terminal** 

```BASH
export SPIRE_CONFIG=/home/usman/docs/spire_feed1.json
export OMNIA_CONFIG=/home/usman/docs/omnia_feed1.json
export GOFER_CONFIG=/home/usman/Videos/oracles/systemd/gofer.json
```
## command to run omnia
>`omnia` 

You can execute either the "feed" or "relay" omnia according to the configuration file.
## sample config for Feed

 ``` json
 {
    "mode": "feed",
    "ethereum": {
      "type": "ethereum",
      "from": "0xD260517CE442379231A7Fe62FD555D2E345F316f",
      "keystore": "/home/usman/.ethereum/keystore",
      "password": "/home/usman/.dapp/testnet/8545/keystore/pass",
      "network": "http://127.0.0.1:8545"
    },
    "options": {
      "interval": 60,
      "msgLimit": 35,
      "srcTimeout": 10,
      "setzerTimeout": 10,
      "setzerCacheExpiry": 120,
      "setzerMinMedian": 1,
      "setzerEthRpcUrl": "http://geth.local:8545"
    },
    "sources":["setzer"],
    "transports":["spire"],
    "pairs": {
      "ETHUSD":{"msgExpiration":1800,"msgSpread":0.5}
    }
  }
  
  ```
  
## sample config for Relay

>you should have to export these env variable in your terminal 

export SPIRE_CONFIG=/home/usman/docs/spire_feed1.json
export OMNIA_CONFIG=/home/usman/docs/omnia_feed1.json
export GOFER_CONFIG=/home/usman/Videos/oracles/systemd/gofer.json
 ``` json
 {
  "mode": "relay",
  "ethereum": {
    "type": "ethereum",
    "from": "0xD260517CE442379231A7Fe62FD555D2E345F316f",
    "keystore": "/home/usman/.dapp/testnet/8545/keystore",
    "password": "/home/usman/.dapp/testnet/8545/keystore/pass",
    "network": "http://127.0.0.1:8545",
    "gasPrice": {
      "source": "node",
      "maxPriceMultiplier": 2,
      "tipMultiplier": 1,
      "priority": "fast"
    }
  },
  "sources":[],
  "transports":["spire"],
  "feeds": [
    "0xD260517CE442379231A7Fe62FD555D2E345F316f",
"0x70997970C51812dc3A010C7d01b50e0d17dc79C8",
"0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266"
  ],
  "options": {
    "interval": 60,
    "msgLimit": 35,
    "verbose": true,
    "logFormat": "text"
  },
  "pairs": {
  "ETHUSD": {
      "msgExpiration": 1800,
      "oracle": "0xD9293f40c60eB292bbCF987138AB59F5CfF9A696",
      "oracleExpiration": 14400,
      "oracleSpread": 3
    }
  }
}
  
  ```
  
  
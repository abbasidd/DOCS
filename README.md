# **ORACLES**

## Architecture
Goals of this new architecture are:

1. Scalability,
2. Reduce costs by minimizing number of ethereum transactions and operations performed on-chain,
3. Increase reliability during periods of network congestion,
4. Reduce latency to react to price changes,
5. Make it easy to on-board price feeds for new collateral types, and
6. Make it easy to on-board new Oracles.

## Design Goals


There are currently two main modules:

### Feed

Each Feed runs a Feed client which pulls prices redundantly with [Setzer]() and [Oracle Suite/Gofer](https://github.com/block360/oracle-suite.git), signs them with an ethereum private key, and broadcasts them as a message to the redundant p2p networks (e.g. scuttlebutt and libp2p).

### Relay

Relays monitor the broadcast messages, check for liveness, and homogenize the pricing data and signatures into a single ethereum transaction to be posted to the on-chain oracles.

## [Live Goerli Oracles]

See [./omnia/config/relay-goerli.conf](./omnia/config/relay-goerli.conf)

| Pair         | Oracle    |
|--------------|-----------|
| AAVE/USD | 0x48d9b9B980EcB23601E4cE5D0f828Ad1F3c8673f |

## [Live Mainnet Oracles]

See [./omnia/config/relay.conf](./omnia/config/relay.conf)

| Pair         | Oracle    |
|--------------|-----------|
| BTC/USD | 0xe0F30cb149fAADC7247E953746Be9BbBB6B5751f |


## Query Oracle Contracts

Query Oracle price Offchain

```
rawStorage=$(cast storage <ORACLE_CONTRACT> 0x1)
cast --from-wei $(cast --to-dec ${rawStorage:34:32})
```

Query Oracle Price Onchain

```
cast --from-wei $(cast --to-dec $(cast call <ORACLE_CONTRACT> "read()(uint256)"))
```

This will require the address you are submitting the query from to be whitelisted in the Oracle smart contract.


### Their Architecture
![plot](./old_architecture.png)
### Our Architecture
![plot](./new_architecture.png)
>Oracle client written in bash that utilizes secure scuttlebutt for offchain message passing along with signed price data to validate identity and authenticity on-chain.
# GOFER 

## Installation
**##PREREQUISITE** It is recommended that you install Go version 1.18.1 on your system.:

1 . git clone https://github.com/block360/oracle-suite.git

2 . cd oracle-suite

3 . RUN this export BUILD_DIR=`<Your bin path>` make; //like /usr/bin/ 

This point pertains to the second point. If you do not specify a build directory, the binary will be created in the current project directory, and you can navigate to it and run it using './gofer agent'.

This will install gofer with updated source code that retrieves pricing data from our rate API.

## Running the gofer 

> Gofer consists of two components that function like a client/server architecture: the gofer agent (server) and the gofer. The gofer agent should be running in order for the gofer to be able to pull prices from it.

**For running the the agent**:
`gofer agent -c ./gofer.json --log.verbosity info`

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
      "balancerV2": {
        "type": "balancerV2",
        "name": "balancerV2",
        "params": {
          "symbolAliases": {
            "ETH": "WETH"
          },
          "contracts": {
            "WETH/GNO": "0xF4C0DD9B82DA36C07605df83c8a416F11724d88b",
            "Ref:RETH/WETH": "0xae78736Cd615f374D3085123A210448E74Fc6393",
            "RETH/WETH": "0x1E19CF2D73a72Ef1332C882F20534B6519Be0276",
            "STETH/WETH": "0x32296969ef14eb0c6d29669c550d4a0449130230",
            "WETH/YFI": "0x186084ff790c65088ba694df11758fae4943ee9e"
          }
        }
      },
      "bittrex": {
        "type": "bittrex",
        "name": "bittrex",
        "params": {
          "symbolAliases": {
            "REP": "REPV2"
          }
        }
      },
      "curve": {
        "type": "curve",
        "name": "curve",
        "params": {
          "contracts": {
            "RETH/WSTETH": "0x447Ddd4960d9fdBF6af9a790560d0AF76795CB08",
            "ETH/STETH": "0xDC24316b9AE028F1497c275EB9192a3Ea0f67022"
          }
        }
      },
      "openexchangerates": {
        "type": "openexchangerates",
        "name": "openexchangerates",
        "params": {
          "apiKey": "API_KEY"
        }
      },
      "poloniex": {
        "type": "poloniex",
        "name": "poloniex",
        "params": {
          "symbolAliases": {
            "REP": "REPV2"
          }
        }
      },
      "sushiswap": {
        "type": "sushiswap",
        "name": "sushiswap",
        "params": {
          "symbolAliases": {
            "ETH": "WETH",
            "BTC": "WBTC",
            "USD": "USDC"
          },
          "contracts": {
            "YFI/WETH": "0x088ee5007c98a9677165d78dd2109ae4a3d04d0c"
          }
        }
      },
      "uniswap": {
        "type": "uniswap",
        "name": "uniswap",
        "params": {
          "symbolAliases": {
            "ETH": "WETH",
            "BTC": "WBTC",
            "USD": "USDC"
          },
          "contracts": {
            "WETH/USDC": "0xb4e16d0168e52d35cacd2c6185b44281ec28c9dc",
            "LEND/WETH": "0xab3f9bf1d81ddb224a2014e98b238638824bcf20",
            "LRC/WETH": "0x8878df9e1a7c87dcbf6d3999d997f262c05d8c70",
            "PAXG/WETH": "0x9c4fe5ffd9a9fc5678cfbd93aa2d4fd684b67c4c",
            "BAL/WETH": "0xa70d458a4d9bc0e6571565faee18a48da5c0d593",
            "YFI/WETH": "0x2fdbadf3c4d5a8666bc06645b8358ab803996e28"
          }
        }
      },
      "uniswapV3": {
        "type": "uniswapV3",
        "name": "uniswapV3",
        "params": {
          "symbolAliases": {
            "BTC": "WBTC",
            "ETH": "WETH",
            "USD": "USDC"
          },
          "contracts": {
            "GNO/WETH": "0xf56d08221b5942c428acc5de8f78489a97fc5599",
            "LINK/WETH": "0xa6cc3c2531fdaa6ae1a3ca84c2855806728693e8",
            "USDC/WETH": "0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640",
            "YFI/WETH": "0x04916039b1f59d9745bf6e0a21f191d1e0a84287"
          }
        }
      },
      "wsteth": {
        "type": "wsteth",
        "name": "wsteth",
        "params": {
          "contracts": {
            "WSTETH/STETH": "0x7f39C581F595B53c5cb19bD0b3f8dA6c935E2Ca0"
          }
        }
      }
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
  # Installation

1 . git clone https://github.com/block360/setzer.git


# Setzer MCD

Query USD price feeds

`cat $(which omnia)` and open the parent dictory in last line command 
You shuld have to change the setzer path in nix store where omnia is present
file name that will be changed : exec/source-setzer and bin/omnia
add `/usr/local/bin/setzer` in PATH enviroment variable.
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


## Installation

Dependencies:

* GNU [bc](https://www.gnu.org/software/bc/)
* [curl](https://curl.haxx.se/download.html)
* GNU [datamash](https://www.gnu.org/software/datamash/)
* GNU `date`
* [jshon](http://kmkeen.com/jshon/)
* GNU `timeout`

Install via make:

* `make link` -  link setzer into `/usr/local`
* `make install` -  copy setzer into `/usr/local`
* `make uninstall` -  remove setzer from `/usr/local`

## Configuration

* `SETZER_CACHE` - Cache directory (default: ~/.setzer)
* `SETZER_CACHE_EXPIRY` - Cache expiry (default: 60) seconds
* `SETZER_TIMEOUT` - HTTP request timeout (default: 10) seconds

## wstETH pair requirement

Due to process of pulling details from mainnet for getting price information.
You need to set `ETH_RPC_URL` environemnt variable. By default it will point to `http://127.0.0.1:8545`.

Example of usage: 

```bash
export ETH_RPC_URL="https://mainnet.infura.io/v3/fac98e56ea7e49608825dfc726fab703"
```

### Fx/Exchangerates API Key
Since latest changes in Exchangerates API, now it requires API key.
To set API Key for this exchange you can use `EXCHANGERATES_API_KEY` env variable. 

Example:

```bash
$ EXCHANGERATES_API_KEY=your_api_key setzer fx krwusd
```

### E2E tests
E2E tests for setzer are written in Go language and relies on [Smocker](https://smocker.dev) for API manipulation.

#### How to setup tests environment
You have to install Docker on your machine first.
Then you will have to start [smocker](https://smocker.dev) container.

```bash
$ docker run -d \
  --restart=always \
  -p 8080:8080 \
  -p 8081:8081 \
  --name smocker \
  thiht/smocker
```

Next step will be to build setzer E2E docker container:

```bash
$ docker build -t setzer -f e2e/Dockerfile .
```

Run newly created container:

```bash
$ docker run -i --rm --link smocker setzer
```

If you need to write tests or want to continuesly run them while doing something you might use docker interactive mode.

```bash
$ docker run -it --rm -v $(pwd):/app --link smocker setzer /bin/bash
```

It will start docker in interactove mode and you will be able to run E2E tests using command: 

```bash
$ go test -v -parallel 1 -cpu 1 ./...
```


  # **SPIRE**

This is based on libp2p which is a peer-to-peer networking protocol designed to enable decentralized communication and file sharing over the internet. It is a modular, open-source networking protocol that allows nodes to communicate with each other directly, without the need for a central server or infrastructure.

**spire have config file which consists of different attributes**
```json 
"transport":{
      "libp2p": {
        "directPeersAddrs":[]}}
  ```

 this attribute is reponsible for the making the peers, So we have to give the info like `/ip4/192.168.18.109/tcp/37705/p2p/12D3KooWPFpaE13gph8p6jdNGJv1M6fwDro8kdst53MUzVpuSJUL` i.e **"\<ip-version>/\<host>/\<protocol>/\<port>/\<type>/\<peer_id>"** w.r.t the quorum of median. 

### command to run spire
**spire agent -c /home/usman/docs/spire_feed2.json --log.verbosity debug**

### how it will work

 we should run the 3 feeds with the 3 spires 
 every feed should have their own omnia
 means the config file have the right omnia addr pasted in feed object of spire's config



# **1. FEED**
# Omnia
>`git clone https://github.com/chronicleprotocol/omnia.git`

>`cd omnia` 
## Quickstart

Some convenience targets for `make` are available. If you have Docker installed, you can do

```
make build          # build all the images
make run            # run images after they're built
make test           # build and run integration tests
```

> So  You should have spire, you will get it from this repository: https://github.com/makerdao/oracle-suite.git. After that you have to configure it. the guide is in the mentioned file named spire.md and sample config file named spire_feed1.json.

>**you should have to export these env variable in your terminal** 

```BASH
export SPIRE_CONFIG=/home/usman/docs/spire_feed1.json
export OMNIA_CONFIG=/home/usman/docs/omnia_feed1.json
export GOFER_CONFIG=/home/usman/Videos/oracles/systemd/gofer.json
```
## command to run omnia
>`omnia` 

You can execute either the "feed" or "relay" command according to the configuration file.
## sample config for Feed

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
  
  # **RELAY**
  # Relayer

>you should have to export these env variable in your terminal 

export SPIRE_CONFIG=/home/usman/docs/spire_feed1.json
export OMNIA_CONFIG=/home/usman/docs/omnia_feed1.json
export GOFER_CONFIG=/home/usman/Videos/oracles/systemd/gofer.json
## command to run omnia
``` BASH 
omnia 
```

# sample config 

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
  
  
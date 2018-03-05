# lx-parity-side-chain

This document describes how to configure private parity-based network (sidechain) and connect to it.

## Bootnode

It is an initial step. Bootnode is needed only for sidechain initiators.

Download and install [Geth & Tools 1.7.0](https://ethereum.github.io/go-ethereum/downloads/)

```
./bin/bootnode --genkey=boot.key
./bin/bootnode --nodekey=boot.key --addr="127.0.0.1:30400"
```

Share the key stored in boot.key file with newowrk participants.

## Parity

Firs of all you need to install Parity client.

There are a few ways of proceeding here. You can build Parity from the sources; you can install Parity from our [binary releases](https://github.com/paritytech/parity/releases) for Ubuntu, Mac/Homebrew and Windows or, if you're on an Ubuntu Snappy platform, just use our Snappy App.

Please refer to [this page](https://github.com/paritytech/parity/wiki/FAQ:-Building,-Installation,-and-Testing) for more details.

Everything described here is tested with v1.8.9-stable.

## Node

### Basic config file

```
[parity]
chain = "side-chain-demo.json"
base_path = "/home/ave/nodes/node1/"
[network]
port = 30300
bootnodes = ["enode://ec96869b5a4c06020de93892a0b0a22601cfc61f0c67393487ff53a7fcf34cec77be24e37d54c4c2f6ba378d365708f68cb4c7a814eac100b0026795078e79e8@127.0.0.1:30400"]
[rpc]
port = 8540
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[ui]
port = 8180
[websockets]
port = 8450
#[account]
#password = ["node.pwds"]
#[mining]
#engine_signer = "0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2"
#reseal_on_txs = "none"

```

### Configure your chain

Setup right bootnode link in node.toml

```
[network]

bootnodes = [....]

```

### Start your own parity node

```
parity --config=node1.toml
```

### Create account

You will most probably need to an account. Setup your own private key. Or create a new one:

```
curl --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["TODO_PHRASE", "TODO_PASS"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540

{"jsonrpc":"2.0","result":"0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2","id":0}
```

Add your password to file `node.pwds` and change config file:

```
[account]
password = ["node.pwds"]
```

### Mining

To became a miner, add section `mining` in config file and restart the node:

```
[mining]
engine_signer = "0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2"
reseal_on_txs = "none"
```

where `engine_signer` is your address.

## Misc

### Check balances
```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x004ec07d2329997267ec62b4166639513386f32e", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x00a94ac799442fb13de8302026fd03068ba6a428", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x002e28950558fbede1a9675cb113f0bd20912019", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

```

# lx-parity-side-chain

## Parity

v1.8.9-stable

## Bootnode

Download and install [Geth & Tools 1.7.0](https://ethereum.github.io/go-ethereum/downloads/)

```
./bin/bootnode --genkey=boot.key
./bin/bootnode --nodekey=boot.key --addr="127.0.0.1:30400"
```

## Node 1

```
parity --config=node1.toml
```

## Node 2

```
parity --config=node2.toml
```

## Node 3

```
parity --config=node3.toml
```

## Misc
### Create account
```
curl --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["node1", "pa$$_node1!"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540

{"jsonrpc":"2.0","result":"0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2","id":0}
```

### Check balances
```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x004ec07d2329997267ec62b4166639513386f32e", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x00a94ac799442fb13de8302026fd03068ba6a428", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x002e28950558fbede1a9675cb113f0bd20912019", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2", "latest"],"id":1}' -H "Content-Type: application/json" localhost:8540

```

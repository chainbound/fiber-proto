# `fiber-proto`
Protobuf files used by the Fiber gRPC API.

## Usage
1. **Add this repo as a submodule in your package:**
```bash
git submodule add https://github.com/chainbound/fiber-proto
```

2. **Use the files to generate the protobuf & gRPC code. Check out the documentation at [grpc.io](https://grpc.io/docs/languages/).**
3. **Create the wrapper methods for the [`api`](api.proto) rpc interface:**
```proto
service API {
    rpc SubscribeNewTxs (TxFilter) returns (stream eth.Transaction) {}
    rpc SubscribeNewBlocks (BlockFilter) returns (stream eth.Block) {}
    rpc SendTransaction (eth.Transaction) returns (TransactionResponse) {}
    rpc SendRawTransaction(RawTxMsg) returns (TransactionResponse) {}
    // Backrun is the RPC method for backrunning a transaction.
    rpc Backrun(BackrunMsg) returns (TransactionResponse) {}
}
```

In the wrapper, a couple of things need to happen:
* The function argument should be an Ethereum object from the most common library in your language. For example, for JS this will be `ethers` / `web3.js`,
for Golang it's `go-ethereum`, Rust would be `ethers-rs`, and for Python it's [`ethereum`](https://pypi.org/project/ethereum/). This means that internally, the library object will have to be converted to an `eth` protobuf object.
* The API key should be added to every request's metadata using the `x-api-key` tag. The way to do this will differ in each language.

**Important**: because native transaction signing is pretty slow in most languages, there is also an endpoint for sending a raw transaction.
This is useful for when the user is using a web3 library that's connected to their node, and uses that to sign transactions. This usually
returns a raw signed transaction (bytes or a hex string), and that's why we have the `SendRawTransaction` RPC as well.

## Examples
The existing implementation in [Go](https://github.com/chainbound/fiber-go) and [TypeScript](https://github.com/chainbound/fiber-ts) can help you get started.

# iOS Applications

This is an overview on how to interface with the Ontology blockchain in an iOS application. There are a few different SDKs available, but in this tutorial we will be specifically using [neovmUtils](https://github.com/Ryucoin/neovm-utils).

## Overview

`neovmUtils` offers a wide array of features for interacting with the Ontology blockchain. It uses a compiled version of the main Ontology golang implementation, so there isn't any reimplementation of core features in Swift.

`neovmUtils` offers numerous additional features, including [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) mnemonic phrases, the ability to create QR codes and interfaces for OEP4 and OEP8 assets.

## Getting Started

`neovmUtils` is available for free on Cocoa Pods [here](https://cocoapods.org/pods/neovmUtils). If you have never used Cocoa Pods before, take a look at this [brief guide](https://guides.cocoapods.org/using/using-cocoapods.html).

To add it to your project just add the line `pod 'neovmUtils'` to your `Podfile` and import it into a file with `import neovmUtils`.

An example `Podfile` would look like this:
``` ruby
use_frameworks!
platform :ios, '12.0'

target :'My_App' do
  pod 'neovmUtils'
end
```

`neovmUtils` will be available on Carthage [in the future](https://github.com/Ryucoin/neovm-utils/issues/9).

## Wallets

The `Wallet` class in `neovmUtils` is the same as the Ontology account object defined in the [wallet specification](https://github.com/ontio/ontology-ts-sdk/blob/master/docs/en/Wallet_File_Specification.md). A full `OntologyWallet` object is currently [under development](https://github.com/Ryucoin/neovm-utils/issues/15). The full documentation for the `Wallet` class is available [here](https://github.com/Ryucoin/neovm-utils/blob/master/docs/wallet.md).

### Creating a Wallet

Wallet creation is quite simple, just call `newWallet()`.

``` swift
let wallet: Wallet = newWallet()
print(wallet.address)
```

If you have a pre-existing wif or private key you can create a wallet with either:
``` swift
let wallet: Wallet? = walletFromWIF(wif: "<WIF HERE>")
```
or
``` swift
let wallet: Wallet? = walletFromPrivateKey(privateKey: "<PRIVATE KEY HERE>")
```

### Mnemonics

You can also create a wallet as well as 12 word mnemonic phrase to go along with your wallet.

``` swift
let pair = newWalletMnemonicPair()
let wallet = pair.0
let mnemonic = pair.1
```

You can then recreate the wallet from the mnemonic:
```
let w: Wallet? = walletFromMnemonicPhrase(mnemonic: mnemonic.value)
```

Note: Mnemonics can only be created when first creating a wallet.

### Exporting to a QR

You can create a `QRView` encoding of various wallet keys using the `exportQR` method. It requires the field `key: keyType` and can also take the two fields `frame: CGRect` and `passphrase: String`.

The values of `keyType` are `PrivateKey`, `NEOPrivateKey`, `NEP2`, `WIF`, `Address` and `PublicKey`. If `key` is `.NEP2` then the optional `passphrase` argument to `exportQR` should be used, otherwise it should be ignored.

An example `QRView` created using
```
let w = newWallet()
qrView.generate(code: w.privateKeyString)
```
looks like:

![alt text](../assets/clientApp/ios/qr.png "QR View")

## Using Smart Contracts

Smart contracts can be used in `neovmUtils` in a couple different ways, the simplest being through `ontologyInvoke` and `ontologyInvokeRead`. The first fully invokes the smart contract, while the second does a pre-execution. Both of these functions are used for neovm smart contracts and not native contracts. A neovm contract will be any contract created by a 3rd party developer (including yourself), while a native contract would be something like the ONT or ONG contracts.

### Parameters

Both `ontologyInvoke` and `ontologyInvokeRead` use the [Ontology RPC client](https://github.com/ontio/ontology/blob/master/docs/specifications/rpc_api.md) to communicate with the Ontology blockchain. Specifically, they both call the `sendrawtransaction` (send raw transaction) RPC method. The RPC method requires serialized transaction objects. In order for `neovmUtils` to build these transaction objects, it requires a smart contract hash and a list of arguments. The arguments must be `OntologyParameter` objects.

`OntologyParameter` objects can be created like this:

``` swift
let wallet: Wallet = newWallet()
let addressParam: OntologyParameter = OntologyParameter(type: .Address, value: wallet.address)
```

The accepted types are `Address`, `String`, `Fixed8`, `Fixed9`, `Integer` and `Array`. `Fixed8` is for 8 decimal numbers and `Fixed9` is for 9 decimal numbers. `Address` type is for base58 encoded addresses (the format of the `address` field on the `Wallet` class).

### Pre-Execution

Pre-execution is used to receive the results of the invocation, without actually completing the invocation. You would use this to read values off a contract (getting the balance on an OEP4 token).

The fields that are required are `contractHash`, `method` and `args`.

An example pre-execution can be done like this:

``` swift
let contractHash: String = "a29564a30043d50620e4c6be61eda834d0acc48b"
let method: String = "getTotal"
let args: [OntologyParameter] = []
let res = ontologyInvokeRead(contractHash: contractHash, method: method, args: args)
```

In this example, `res` is the hexadecimal response from the pre-execution. Note that these results are in [little-endian](https://en.wikipedia.org/wiki/Endianness).

### Full Execution

Executing a contract is used to make actual events take place on the blockchain, like transferring an OEP8 asset to another wallet.

In addition to the `contractHash`, `method` and `args` it requires the fields `gasPrice`, `gasLimit`, `wif` and `payer`. `gasPrice` must be at least 500 and `gasLimit` must be at least 20000 - they are dependent on the complexity of the smart contract you are executing. `wif` is the signer of the transaction and `payer` is the address of the wallet that will pay for the transaction. If you aren't sure what you are doing, then `payer` should be the address of the wallet whose wif you are using.

An example execution can be done like this:

``` swift
let wallet: Wallet = newWallet()
let contractHash: String = "c168e0fb1a2bddcd385ad013c2c98358eca5d4dc"
let method: String = "put"
let args: [OntologyParameter] = [OntologyParameter(type: .Address, value: wallet.address), OntologyParameter(type: .String, value: "Hello!")]
let gasPrice: Int = 500
let gasLimit: Int = 20000

let res = ontologyInvoke(contractHash: contractHash, method: method, args: args, gasPrice: gasPrice, gasLimit: gasLimit, wif: wallet.wif, payer: wallet.address)
```

In this example, `res` is the transaction hash - or a blank string if it fails.

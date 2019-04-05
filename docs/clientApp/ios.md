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

## Using Smart Contracts

Smart contracts can be used in `neovmUtils` in a couple different ways, the simplest being through `ontologyInvoke` and `ontologyInvokeRead`. The first fully invokes the smart contract, while the second does a pre-execution.

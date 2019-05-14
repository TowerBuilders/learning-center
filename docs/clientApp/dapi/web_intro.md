# Intro to creating web based dApps

For all web based applications looking to interact with the Ontology blockchain, it is best advised to utilize a dAPI (decentralized API) provider. These providers are existing wallets which expose a standard API to web based applications in order to fetch data from the Ontology blockchain, to request signatures from a users wallet, and to broadcast transactions to the blockchain.

The benefit to using a wallet provider that supports the dAPI is that the developer of the application is provided with a simplified interface for all interactions, and more importantly does not have to worry about managing the users private key in a secure fashion. By offloading the responsibility of private key management, dApp developer don't have to worry about users not trusting the dApp with their private keys, because the dApps never requires access to it, and can operate normally by simply asking the wallet provider to request the users signature on it's behalf.

Here is a high level overview of the flow of interactions between the user, your application, the dAPI provider, and the Ontology blockchain.
![alt text](../assets/clientApp/dapi/dAPIOverview.png "dapi overview")

## dAPI Providers

There are currently two different wallets that provide access to the Ontology dAPI, they are the Cyano wallet and the O3 Desktop wallet. Both of these wallets have separately implemented the [standard interface](https://github.com/ontio/OEPs/pull/8), but each have their own set of fundamental differences. Let's first briefly introduce each wallet provider, and list a few things unique to each provider.

### Cyano Wallet

- Chrome extension
- Simple and minimalistic interface
- Feature light wallet
- Secures user private keys in chrome extension storage
- Interface with websites running in Chrome browser

[Download](https://chrome.google.com/webstore/detail/cyano-wallet/dkdedlpgdmmkkfjabffeganieamfklkm?hl=en)

### O3 Desktop Wallet

- Desktop application (Windows, OSX, Linux)
- Clean interface with dark mode
- Full featured wallet
- Secures user private keys in local native app storage
- Interfaces with websites running in any desktop browser (Chrome, Firefox, Brave, Safari, Edge, Opera)
- Interfaces with other native desktop applications/games
- Wallet and dAPI integrations for both Ontology & NEO
- Full featured development support testbed
- Dedicated dAPI development documentation
- dAPI support for staking
- Plugin style dAPI packages to allow for wrapper plugins

[Download](https://o3.network/)

For the purposes of these dAPI tutorials we will be using the O3 wallet integration for it's developer tooling, broader platform support, and larger userbase. However, when developing any dApp it will be important to add support for all dAPI enabled wallet providers to reach the maximum amount of possible users.

## dAPI Javascript Packages

In order to interface with the dAPI the dApp developer can needs to include the appropriate Javascript package into their application. O3 provides availability to include the required packages either via CDN or NPM install. Note that O3 runs a plugin style set of packages to support various different blockchains and utility packages, so multiple packages will be required to be installed. For the full suite of available packages, please check out their [guide](https://github.com/O3Labs/o3-dapi).

CDN
```html
<script src="https://cdn.jsdelivr.net/npm/o3-dapi-core/lib/o3-dapi-core.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/o3-dapi-ont/lib/o3-dapi-ont.min.js"></script>
```
Initialize with:
```Javascript
// window.o3dapi
// window.o3dapiOnt
o3dapi.initPlugins([o3dapiOnt]);
```

npm
```
npm i --save o3-dapi-core
npm i --save o3-dapi-ont

or

yarn add o3-dapi-core
yarn add o3-dapi-ont
```

Initialize with:

ES5 or node.js
```
var o3dapi = require('o3-dapi-core');
var o3dapiOnt = require('o3-dapi-ont');

o3dapi.initPlugins([o3dapiOnt]);
```

ES6
```
import o3dapi from 'o3-dapi-core';
import o3dapiOnt from 'o3-dapi-ont';

o3dapi.initPlugins([o3dapiOnt]);
```

## Basic dAPI Interactions: Assets

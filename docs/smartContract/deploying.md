# Deploying Smart Contracts

After you have finished writing and fully testing your smart contract in the SmartX web IDE, you are now ready to deploy your smart contract. There are several options available when deploying your smart contract, and each has it's own specific purpose.

## Available Networks Overview

### Private Net

A private instance of the Ontology blockchain, running locally on your computer. This should always be the very first place that you deploy your smart contract after running all your functions through the debugger in SmartX. Once deployed on your private network, you can start creating your client or server based application that will interact with your smart contract. The locally hosted private network has the same REST/RPC/Websocket interfaces available as the Ontology Test or Main net, and can be used and should be used whenever possible during the development phase. Only once you have fully built out and tested both your smart contract and client/server application against your contract deployed on your private network, should you start looking into deploying to the Ontology test network.

### Test Net

Since your private net is hosted on your local machine it makes it harder to share the beta version of your app, so this is when the Ontology hosted test network comes into the picture. The test network is a full featured version of the Ontology blockchain, which even has a fully hosted version of the web explorer. Test network ONT/ONG is provided via an automated system, and is only limited on a per address, per day basis. However, you can create as many new addresses as you required to deploy and test out your contract and application. Once you have completed testing with your team, and are ready to bring your application to the masses, it's time to deploy to the Ontology main net.

### Main Net

The production version of the Ontology blockchain is an open protocol on which any user is free to deploy their smart contract applications, as long as they pay the fee in ONG. A small to medium sized contract will roughly costs only 10 ONG to deploy, which makes it very easy for even individual project owners to get started. Once on deployed on the main net, your contract will be able to fully interact with the community of other hosted smart contracts and user accounts.

## Setting Up a Private Net

The ontology team has made it extremely simple for anyone to host their own private net by installing a single pre-packaged desktop application. The private net application is called Solo Chain, and it can be run without any environment setup or external dependencies to install. Simply download the Solo Chain application from the official repository on GitHub, and start the application.

Download the latest version of Solo Chain from the link below:
https://github.com/punicasuite/solo-chain/releases

Upon installation, open the Solo Chain application. You should now be on the `Accounts` tab of the application. Where there will be a single "master" account, which holds all the ONT for the network.

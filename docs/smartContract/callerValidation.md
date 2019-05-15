# Smart Contracts: Caller Validation

This is an overview on validating the caller in Ontology smart contracts. It will go over all you need to know to validate who the smart contract caller is in a neoVM smart contract.

## Background

Transactions on the blockchain can be signed by a wallets private key. The private key leaves a publicly verifiable signature stored in the transaction object itself. For reference, you can see how this is done in the `addSign` function in the [Type Script SDK here](https://github.com/ontio/ontology-ts-sdk/blob/7fe82ed595ac28448305d86335aa69f17af3990f/src/transaction/transactionBuilder.ts#L96).

When the transaction is invoked on the blockchain, the array of signatures can be verified in smart contracts. This will allow you to validate which wallets can use specific functions in your contract.

## Check Witness

The way to validate who the caller is, is with the `CheckWitness` function. It takes one argument, an address, and checks to see if it is either:

  **1)** A wallet that signed the transaction

  **2)** The contract calling the function (I will talk about this below)

You can view the implementation in the [core Go implementation here](https://github.com/ontio/ontology/blob/197c07962366ecc50a67d4909a3250bb134c506f/smartcontract/smart_contract.go#L164).

### Wallet Validation

Here is an example of using `CheckWitness` to validate that the caller is a specific wallet address.

``` python
from ontology.interop.System.Runtime import Log, CheckWitness

OWNER = Base58ToAddress('ALNdxK654mTcMzex9nsfnBXLEfiryhpNUB')

def Main(operation, args):
  if operation == 'private':
    return private()
  return False


def private():
  if not CheckWitness(OWNER):
    Log('You cannot do that!')
    return False
  else:
    Log('Only the OWNER sees this')
    return True
```

In this example we're doing a few things. First declaring the `OWNER` variable to be the address `ALNdxK654mTcMzex9nsfnBXLEfiryhpNUB`. Next, if the `private` function is called, we call `if not CheckWitness(OWNER)`. If the current transaction hasn't been signed by the `OWNER` address, we're going to see the print `'You cannot do that!'` and if it has been, then we'll see `'Only the OWNER sees this'`.

### Contract Validation

You can also check to see if another contract is running your contract. This involves using the two functions `GetCallingScriptHash` and `GetEntryScriptHash`.

`GetCallingScriptHash` returns the script hash of the most recent invoker of the contract. This means that if a wallet has invoked the contract directly, `GetCallingScriptHash` would return their address. However it also means that if another contract has invoked your contract, then `GetCallingScriptHash` will return that script hash. This means that calling `CheckWitness(GetCallingScriptHash())` should always return true (remember point #2 from before).

`GetEntryScriptHash` returns the script hash of the original invoker. This means that if a wallet has invoked the contract directly, `GetEntryScriptHash` would return their address. If another contract is invoking the contract, then `GetEntryScriptHash` will return the original invoker (of that contract). This also means that calling `CheckWitness(GetEntryScriptHash())` should always return true (remember point #2 from before).

You can then use this to prevent contracts from calling your contract. See this example:

``` python
from ontology.interop.System.Runtime import Log
from ontology.interop.System.ExecutionEngine import GetCallingScriptHash, GetEntryScriptHash

def Main(operation, args):
  if operation == 'private':
    return private()
  return False


def private():
  callerHash = GetCallingScriptHash()
  entryHash = GetEntryScriptHash()
  if callerHash != entryHash:
    Log('Contract cannot call this!')
    return False
  else:
    Log('Only wallets see this!')
    return True
```

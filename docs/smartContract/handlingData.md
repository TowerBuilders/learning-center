# Smart Contracts: Handling Data

This is an overview on handling data in Ontology smart contracts. It will go over all you need to know to master reading/writing data to a neoVM smart contract.

For this tutorial we will be continuing to use the SmartX IDE, writing in Python. We will be using the version 2.0 Python compiler.

## Introducing Storage

All neoVM smart contracts are able to keep track of data stored on the blockchain. This data is siloed off from other contracts and is specific to that contract. It can be written to and read from freely by anyone on the network. It is kept in storage.

We can add storage to smart contracts by adding the functions `GetContext`, `Get`, `Put` and `Delete` from `ontology.interop.System.Storage`.

`GetContext` gets the storage context for the smart contract, meaning it returns the value that needs to be written to and read from in order to keep track of data for a given smart contract. `Get` reads a value from a context for a given key. `Put` sets a value for a key in a context. `Delete` deletes a value for a key in a context.

## Storage Example

Let's take a look at an example contract:
``` python
from ontology.interop.System.Storage import GetContext, Get, Put, Delete
ctx = GetContext()

def Main(operation, args):
    if operation == 'GetItem':
        key = args[0]
        return GetItem(key)
    elif operation == 'PutItem':
        key = args[0]
        value = args[1]
        return PutItem(key, value)
    elif operation == 'DeleteItem':
        key = args[0]
        return DeleteItem(key)
    return False


def GetItem(key):
    return Get(ctx, key)


def PutItem(key, value):
    Put(ctx, key, value)
    return True


def DeleteItem(key):
    Delete(ctx, key)
    return True
```

In this contract we get the context of the contract at the top, we only need this value once. We then define three functions, `GetItem`, `PutItem` and `DeleteItem`. These functions simply wrap the `Get`, `Put` and `Delete` functions we imported at the top.

We can now store values in storage using `PutItem`, retrieve those values using `GetItem` and delete them from storage using `DeleteItem`.

### The Storage Debugger

SmartX offers a storage debugger that we can use to test out our contracts. Click the button labeled "Storage" at the bottom of the screen in SmartX to bring it up. It should look like this:

![alt text](../assets/smartContracts/handlingData/StorageDebugger.png "Storage Debugger")

To the right of the screen we're going to go to the "Run" tab and go to "Select run function" and choose `PutItem` from the dropdown list. For key we'll put the string `Hello, world` and for value the string `Hi there`. It should look like:

![alt text](../assets/smartContracts/handlingData/PutItem.png "Put Item")

Next we'll click "Debug Run" at the bottom of the screen and we'll immediately see that the storage debugger has updated!

![alt text](../assets/smartContracts/handlingData/StoragePut.png "Storage Put")

Now switch the selected function to `GetItem`, click "Debug Run" (make sure that the key is still `Hello, world`) and click on "Logs" at the bottom and we'll see:

![alt text](../assets/smartContracts/handlingData/PrintPut.png "Print Put")

Lastly, if we switch the function to `DeleteItem`, click "Debug Run" (make sure that the key is still `Hello, world`) and then run `GetItem` again, we'll see:

![alt text](../assets/smartContracts/handlingData/PrintDelete.png "Print Delete")

### Breakpoints

You can also use breakpoints to debug a smart contract. Simply click to the left of the line and it will appear red. When you run "Debug Run", a hit breakpoint will turn green and non-hit breakpoints will remain red. For example, using our smart contract from above:
![alt text](../assets/smartContracts/handlingData/Breakpoints.png "Breakpoints")

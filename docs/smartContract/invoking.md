# Smart Contracts: Invoking a Contract

This is an overview on invoking other contracts from your contract. It will cover everything you need to know to get your contract to work with other contracts.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Rol58k4YYcU" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

There are two different ways to invoke another contract: `RegisterAppCall` and `DynamicAppCall`

There are no difference in costs between `RegisterAppCall` and `DynamicAppCall` - so it's completely up to you which to use.

## Register App Call

`RegisterAppCall` is used to statically call a contract from your contract. You define the specific contract to interact with, before deploying your contract. Here is an example contract:

``` python
from ontology.interop.System.App import RegisterAppCall

OtherContract = RegisterAppCall('1a6f62cc0ff3d9ae32b0b924aeda2056a9fdfccb', 'operation', 'args')

def Main(operation, args):
  if operation == 'func':
    return OtherContract(operation, args)
  return False
```

The `RegisterAppCall` function is used when you already know the contract you will be calling.

## Dynamic App Call

`DynamicAppCall` on the other hand is used to call a contract determined after deployment.

Here is an example contract:

``` python
from ontology.interop.System.App import DynamicAppCall
from ontology.libont import hexstring2bytes, bytearray_reverse

def Main(operation, args):
  if operation == 'func':
    address = bytearray_reverse(hexstring2bytes('1a6f62cc0ff3d9ae32b0b924aeda2056a9fdfccb'))
    return DynamicAppCall(address, operation, args)
  return False
```

You can see that the address of the contract being called with `DynamicAppCall` can be determined based on the arguments to your contract. You use `DynamicAppCall` when you don't necessarily know which contract to call before deploying.

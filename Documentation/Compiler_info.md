## Compiler 
Miniscript needs to be compiled to Bitcoin Script in order to be used on the Bitcoin network. To compile the Miniscript logic we previously wrote, you can use a Miniscript compiler such as the `miniscript` library for Python. Here's an example of how you can use it to compile the "adding liquidity" logic we wrote in Miniscript:

```

from bitcoin.miniscript import *

from bitcoin.wallet import CKey, CPubKey, P2WSHBitcoinAddress



# Define the public keys and addresses of the pool creator and the LP provider

pool_creator_pubkey = CPubKey(xxxxxxxx)

lp_provider_pubkey = CPubKey(xxxxxxxx)

pool_creator_address = P2WSHBitcoinAddress.from_redeemScript(

    pool_creator_pubkey.to_redeemScript())

lp_provider_address = P2WSHBitcoinAddress.from_redeemScript(

    lp_provider_pubkey.to_redeemScript())



# Define the logic for adding liquidity

add_liquidity_logic = And(

    Multi(2, [pool_creator_pubkey, lp_provider_pubkey]),

    Hash160(pool_creator_pubkey) + EqualVerify() + 

    10000 + CheckSequenceVerify() + 

    Hash160(lp_provider_pubkey) + EqualVerify() + 

    10000 + CheckSequenceVerify() + 

    CheckSig())



# Compile the Miniscript logic to Bitcoin Script

compiled_script = add_liquidity_logic.compile()



# Print the resulting Bitcoin Script

print(compiled_script.hex())

```
> :warning: Note that you'll need to replace the `xxxxxx` placeholders with the actual public keys you want to use for the pool creator and LP provider. Also, make sure you have the `bitcoin`, `ecdsa`, and `secp256k1` libraries installed in your Python environment to use the `miniscript` library.

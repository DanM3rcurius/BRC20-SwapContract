# Dedicated BRC20 Envelope (Dick-E)
This is an experiment to create a new envelope for fungible tokens on bitcoin in cooperation with ChatGPT.
The goal is to better distribute the load and complexity that fungible tokens add in the ord envelope that was meant for non fungible tokens.
With this envelope **it should be less difficult** to create a DeFi ecosystem on bitcoin, with Swaps, DEXs, loans and all.


<div class="notecard warning">
<h4>Warning</h4>
<p> This is highly experimental tech, tread carefully, you could loose everything to the dragons beyond.</p>
</div>

## A new token envelope
I fed ChatGPT with the ord envelope code and asked to add double spend mitigation and BRC20 backward compatibility. This is what it answered:

Yes, it is possible to add double spend protection to the envelope and make it compatible with the example you provided. Here's an updated version of the envelope that includes double spend protection and is compatible with the example:

```
{
"v": 1,
"contract": {
"name": "BRC20",
"functions": [
{
"name": "deploy",
"inputs": [
{"name": "ticker", "type": "string"},
{"name": "total_supply", "type": "int"}
],
"outputs": []
},
{
"name": "transfer",
"inputs": [
{"name": "to", "type": "address"},
{"name": "amount", "type": "int"}
],
"outputs": []
},
{
"name": "balanceOf",
"inputs": [
{"name": "owner", "type": "address"}
],
"outputs": [
{"name": "balance", "type": "int"}
]
}
],
"events": [
{
"name": "Transfer",
"inputs": [
{"name": "from", "type": "address"},
{"name": "to", "type": "address"},
{"name": "amount", "type": "int"}
]
}
],
"variables": [
{"name": "total_supply", "type": "int"}
]
},
"constraints": [
{
"id": "double-spend-protection",
"type": "locktime",
"value": 144,
"op": "VERIFY"
},
{
"id": "check-script-type",
"type": "eq",
"value": "scripthash",
"op": "VERIFY"
},
{
"id": "check-contract-hash",
"type": "hash160",
"value": "aa0d547424c3237a39b0e53a717da9a35a785b11",
"op": "EQUALVERIFY"
},
{
"id": "check-contract-format",
"type": "checksig",
"op": "VERIFY"
}
]
}
```
#### Here is an overview of how this logic works:
In this version of the envelope, we've added a new constraint with ID "double-spend-protection" that implements a locktime of 144 blocks (approximately 24 hours) to prevent double spending. The "check-script-type" and "check-contract-hash" constraints are the same as before, but we've added a new constraint with ID "check-contract-format" that checks that the contract follows the correct format (using a standard Bitcoin Script CHECKSIG operation).

The contract itself has been updated to include the ticker symbol and total supply inputs in the "deploy" function.

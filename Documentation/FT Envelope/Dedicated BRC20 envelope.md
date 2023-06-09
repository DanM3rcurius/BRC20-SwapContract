# Dedicated BBC20 Envelope (BBC20)
This is an experiment to create a new envelope for fungible tokens on bitcoin in cooperation with ChatGPT.
The goal is to better distribute the load and complexity that fungible tokens add in the ord envelope that was meant for non fungible tokens.
With this envelope **it should be less difficult** to create a DeFi ecosystem on bitcoin, with Swaps, DEXs, loans and all.


<div class="notecard warning">
<h4>Warning</h4>
<p> This is highly experimental tech, tread carefully, you could loose everything to the dragons beyond.</p>
</div>

#### [Here is the migration script documentation](https://github.com/DanM3rcurius/BBC20-SwapContract/blob/main/Documentation/FT%20Envelope/BBC20%20New%20Envelope%20Migration%20Script.md)

## A new token envelope
I fed ChatGPT with the ord envelope code and asked to add double spend mitigation and BBC20 backward compatibility. This is what it answered:

> Yes, it is possible to add double spend protection to the envelope and make it compatible with the example you provided. Here's an updated version of the envelope that includes double spend protection and is compatible with the example:

```json
{
"v": 1,
"contract": {
"name": "BBC20",
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
> In this version of the envelope, we've added a new constraint with ID "double-spend-protection" that implements a locktime of 144 blocks (approximately 24 hours) to prevent double spending. The "check-script-type" and "check-contract-hash" constraints are the same as before, but we've added a new constraint with ID "check-contract-format" that checks that the contract follows the correct format (using a standard Bitcoin Script CHECKSIG operation).

> The contract itself has been updated to include the ticker symbol and total supply inputs in the "deploy" function.

### Q: What would be the advantages and disadvantages of an RBG implementation over the one you composed?

> There are several advantages and disadvantages of an RGB implementation compared to the one I composed. Here are a few of them:

#### Advantages of RGB:
> 1. More flexible asset issuance: RGB allows for the creation of arbitrary asset types with custom attributes and issuance rules, enabling a wide range of use cases beyond simple tokenization.
> 2. Stronger privacy: RGB uses advanced cryptographic techniques such as blinded proofs and confidential transactions to provide stronger privacy for asset holders and transactions.
> 3. Better interoperability: RGB is designed to be interoperable with other blockchain systems and protocols, enabling cross-chain asset swaps and atomic transactions.

#### Disadvantages of RGB:
> 1. More complex: RGB is a more complex system than a simple UTXO-based token implementation, requiring a deeper understanding of cryptography and protocol design.
> 2. More dependencies: To use RGB, you need to use a dedicated RGB wallet or toolkit, which can be an additional dependency and may require more maintenance.
> 3. Less mature: While RGB is actively developed and supported, it is a newer system than Bitcoin and has a smaller user base and ecosystem.

> Overall, the choice between a simple UTXO-based token implementation and RGB depends on the specific use case and requirements. If you need a simple token for basic transactions, the UTXO-based approach may be sufficient. However, if you require more advanced asset management features, stronger privacy, or cross-chain interoperability, RGB may be a better choice.

## deploy
> To deploy the envelope, you can use a Bitcoin transaction to encode it as a Bitcoin script. You would need to send the transaction to the Bitcoin network and broadcast it to miners for confirmation and inclusion in a block. Once the transaction is confirmed, the envelope is deployed and can be used to issue and transfer BBC20 tokens.

## Testing
> To test the envelope, you can use Bitcoin testnet or a private Bitcoin network to simulate a real-world scenario. You can write test cases to check if the envelope works as expected and if it handles various edge cases correctly.

> Here's an example of how you can test the envelope:
1. Deploy the envelope on the Bitcoin network.
2. Issue a certain amount of BBC20 tokens to an address.
3. Transfer some of the BBC0 tokens to another address.
4. Try to transfer more BBC20 tokens than what the address has. This should result in a failure.
5. Attempt to double-spend BBC20 tokens. This should also result in a failure.
6. Test the 24-hour time lock for liquidity removal.
7. Try to remove liquidity before the time lock expires. This should result in a failure.
8. Wait for the time lock to expire and then try to remove liquidity.
9. Test the trading logic by exchanging BBC20 tokens for ABC tokens and vice versa.
10. Test edge cases such as attempting to trade more tokens than what is available, attempting to remove liquidity when there is no liquidity, and so on.

>By testing the envelope thoroughly, you can ensure that it works as expected and that it is secure and reliable.

### Test Cases
> Test Case 1: Deploying the BBC-20 Token Envelope
- Send a deployment transaction with the following information:
- p: "BBC-20"
- op: "deploy"
- tick: "ordi"
- max: "21000000"
- lim: "1000"
- Verify that the transaction is accepted by the network and the BBC-20 token envelope is deployed.

> Test Case 2: Minting BBC-20 Tokens
- Send a minting transaction with the following information:
- p: "BBC-20"
- op: "mint"
- amt: "100"
- Verify that the transaction is accepted by the network and 100 BBC-20 tokens are minted to the specified address.

> Test Case 3: Transferring BBC-20 Tokens
- Send a transfer transaction with the following information:
- p: "BBC-20"
- op: "transfer"
- to: "<receiver_address>"
- amt: "50"
- Verify that the transaction is accepted by the network and 50 BBC-20 tokens are transferred from the sender's address to the receiver's address.

> Test Case 4: Burning BBC-20 Tokens
- Send a burning transaction with the following information:
- p: "BBC-20"
- op: "burn"
- amt: "25"
- Verify that the transaction is accepted by the network and 25 BBC-20 tokens are burned from the sender's address.

> Test Case 5: Retrieving BBC-20 Token Balance
- Send a balance query with the following information:
- p: "BBC-20"
- op: "balance"
- Verify that the response contains the correct BBC-20 token balance for the specified address.

Test Case 6: Retrieving BBC-20 Token Total Supply
- Send a total supply query with the following information:
- p: "BBC-20"
- op: "total-supply"
- Verify that the response contains the correct BBC-20 token total supply.

> Test Case 7: Retrieving BBC-20 Token Maximum Supply
- Send a maximum supply query with the following information:
- p: "BBC-20"
- op: "max-supply"
- Verify that the response contains the correct BBC-20 token maximum supply.

> Test Case 8: Retrieving BBC-20 Token Circulating Supply
- Send a circulating supply query with the following information:
- p: "BBC-20"
- op: "circ-supply"
- Verify that the response contains the correct BBC-20 token circulating supply.

> Test Case 9: Retrieving BBC-20 Token Transfer History
- Send a transfer history query with the following information:
- p: "BBC-20"
- op: "transfer-history"
- from: "<start_block>"
- to: "<end_block>"
- Verify that the response contains the correct transfer history for the specified time range.

> Test Case 10: Retrieving BBC-20 Token Holder List
- Send a holder list query with the following information:
- p: "BBC-20"
- op: "holder-list"
- Verify that the response contains the correct list of BBC-20 token holders and their balances.


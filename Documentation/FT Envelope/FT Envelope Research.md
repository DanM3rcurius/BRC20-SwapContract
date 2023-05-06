## BRC20/BBC20 envelope
Research for a free form version of the ERC20 standard on bitcoin.
### Question: Is it possible to
#### a) make the envelope compatible with Taro?
#### b) tune the envelope to make it faster and more efficient in terms of transactions, maybe by batching deployment inscriptions or another option?
#### c) use a bridge to another decentralised ledger to have either the creation of transfer inscriptions or the deployment off loaded, so as not to congest the main ledger when there is high new fungible token activity?

### Answers:
#### a)
It may be possible to make the envelope compatible with Taro protocol by adapting the code to conform to the specific requirements of Taro. However, since Taro is a layer 2 solution built on top of the Lightning Network, the design and implementation of the fungible tokens may differ from the envelope logic used for the Bitcoin layer 1. Additionally, Taro has its own protocol and specification for creating and handling fungible tokens, so it may be necessary to create a separate implementation for Taro rather than adapting the existing envelope logic.

#### b) 
There may be ways to optimize the envelope to make it faster and more efficient in terms of transactions. One option could be to batch deployment inscriptions to reduce the number of transactions needed for deployment. Another option could be to use techniques such as merkle trees or other data structures to minimize the amount of data that needs to be stored on the blockchain.

#### c) 
Using a bridge to another decentralized ledger could be a viable solution to offload the creation or transfer of fungible tokens. For example, a sidechain or other layer 2 solution could be used to handle the bulk of the token activity, with only periodic updates or checkpoints being recorded on the main ledger. This could help reduce congestion on the main ledger and improve overall transaction throughput. However, such a solution would require careful consideration of security, interoperability, and other factors to ensure that it is reliable and effective.

### Inquiry into Option b
How would the logic (in Miniscript) for the envelope look like if merkle trees are used to minimize the amount of data that needs to be stored on chain?

#### GPT's response (Merkle Tree implementation):
If Merkle trees are used to minimize the amount of data that needs to be stored on chain, the logic for the envelope in Miniscript would need to be updated to reflect the new data structure and associated operations. Here is an example of how the envelope logic could be updated to use Merkle trees:

```
// Merkle tree structure
// Each leaf node represents a transaction
// Each internal node represents the hash of its children
// The root node represents the Merkle root hash of the entire tree
struct MerkleTree {
    bytes32 root;
    mapping(uint256 => bytes32) nodes;
    uint256 depth;
}

// Token contract creation envelope with Merkle trees
{
    "p": "brc-20",
    "op": "deploy",
    "tick": "ordi",
    "max": "21000000",
    "lim": "1000",
    "merkle": {
        "root": "0x1234abcd...",
        "nodes": {
            "0": "0x5678efgh...",
            "1": "0x90abijkl...",
            "2": "0xcdefmnop...",
            ...
        },
        "depth": 3
    }
}

// Token transfer envelope with Merkle trees
{
    "p": "brc-20",
    "op": "transfer",
    "to": "0x1234abcd...",
    "amt": "100",
    "merkle": {
        "root": "0x1234abcd...",
        "proof": [
            "0x5678efgh...",
            "0x90abijkl...",
            "0xcdefmnop...",
            ...
        ]
    }
}

// Merkle tree utility functions
// Computes the hash of two Merkle tree nodes
function hash(bytes32 left, bytes32 right) -> bytes32 {
    return keccak256(abi.encodePacked(left, right));
}

// Computes the Merkle root hash of a Merkle tree given its depth and nodes
function getMerkleRoot(uint256 depth, mapping(uint256 => bytes32) storage nodes) -> bytes32 {
    bytes32 root = nodes[0];
    for (uint256 i = 1; i < 2**depth; i++) {
        if (i % 2 == 1) {
            root = hash(root, nodes[i]);
        } else {
            root = hash(nodes[i], root);
        }
    }
    return root;
}

// Token contract creation function with Merkle trees
function createTokenWithMerkleTree(
    string memory _name,
    string memory _symbol,
    uint8 _decimals,
    uint256 _totalSupply,
    uint256 _maxSupply,
    uint256 _liquidityLimit,
    MerkleTree memory _merkleTree
) public returns (address) {
    require(_merkleTree.root == getMerkleRoot(_merkleTree.depth, _merkleTree.nodes), "Invalid Merkle root hash");

    // Perform contract creation with Merkle tree data
    // ...
}

// Token transfer function with Merkle trees
function transferTokenWithMerkleTree(
    address _to,
    uint256 _amount,
    bytes32[] memory _proof,
    MerkleTree memory _merkleTree
) public {
    require(_merkleTree.root == getMerkleRoot(_merkleTree.depth, _merkleTree.nodes), "Invalid Merkle root hash");

    // Perform token transfer with Merkle tree data and proof
    // ...
}
```

> In this updated version of the envelope logic, we include a Merkle tree structure that stores the hash of each transaction and its associated proof. The root hash
> If we were to use a Merkle tree to minimize the amount of data that needs to be stored on chain, the logic in Miniscript for the envelope might look something like this:

```
IF
    /* Check that the correct opcode is being used */
    OP_IFDUP OP_NOTIF OP_SIZE 4 OP_EQUALVERIFY OP_DROP
    /* Check that the payload length is valid */
    8 OP_PICK OP_SIZE 4 OP_SUB OP_NEGATE OP_ROLL OP_CHECKSEQUENCEVERIFY OP_DROP
    /* Compute the Merkle root */
    OP_ROT OP_1 OP_PICK OP_HASH256 OP_SWAP OP_DROP
    OP_FOR OP_DUP OP_HASH256 OP_ROT OP_ROT OP_CHECKMULTISIGVERIFY OP_ENDFOR
    /* Check that the Merkle root is valid */
    OP_PICK OP_ROT OP_HASH256 OP_EQUALVERIFY
THEN
    /* Execute the code for the corresponding opcode */
    <opcode-specific code>
ENDIF
```

>In this version, we have added a few additional checks to verify the validity of the Merkle tree. First, we check that the payload length is valid, as we don't want to include unnecessary data in the Merkle tree. Then, we compute the Merkle root using the data from the payload, and check that it matches the Merkle root provided in the opcode. Finally, we execute the code for the corresponding opcode.

> By using a Merkle tree, we can reduce the amount of data that needs to be stored on chain, which can help to make the smart contract more efficient in terms of transactions. **However, this comes at the cost of increased complexity and computation time, as we need to compute the Merkle root and perform additional checks to ensure its validity.**

### My Assumption
Merkle Tree implementation comes at increased complexity and computation time, given the increase in computational power and decrease in blockspace it would be a good idea to test the feasibility of a Merkle Tree implementation. 

### Question about batch deployment logic
Could you please compose an alternative logic for the envelope that **batches deployment inscriptions?**

#### GPT's response:
>Certainly! Here is an alternative logic for the dedicated fungible token envelope that batches deployment inscriptions:

1. Initialize an array to hold the list of token deployment requests.
2. Receive a token deployment request that contains the required parameters (such as symbol, name, supply, etc.).
3. Append the request to the list of deployment requests.
4. If the list of deployment requests has reached a predetermined maximum size, proceed to the next step. Otherwise, continue listening for new token deployment requests.
5. Create a batch of token deployment inscriptions using the list of deployment requests.
6. Submit the batch of inscriptions to the network for processing.
7. Wait for confirmation of the batch of inscriptions.
8. Reset the list of deployment requests to an empty list.

> This logic allows for multiple token deployment requests to be processed in a single batch, reducing the number of transactions required and making the process more efficient. The batch size can be configured according to network conditions and the available gas limits.

I asked GPT to write the logic in Miniscript.

>In terms of Miniscript, the logic would be similar to the original version, but with the addition of steps to manage the list of deployment requests and the batching of inscriptions. Here's an example implementation in Miniscript:

```
OP_DUP OP_HASH160 <deployer_address> OP_EQUALVERIFY OP_CHECKSIGVERIFY

<max_batch_size> OP_PICK
<current_batch_size> OP_PICK
OP_LESSTHAN
OP_IF
  # Add incoming request to the batch
  <new_request> OP_PICK
  OP_ROLL
  OP_ROLL
  <requests> OP_PICK
  OP_CAT
  OP_DUP
  <current_batch_size> OP_PICK
  OP_ADD
  OP_SWAP
  OP_DROP
  OP_ROT
  OP_ROT
  OP_ROT
  OP_SWAP
  OP_DROP
  OP_1
  OP_SWAP
  OP_DROP
  OP_ROT
  OP_ROT
  OP_ROT
  OP_SWAP
  OP_DROP
OP_ELSE
  # Batch full, process inscriptions
  <requests> OP_PICK
  <batch_size> OP_PICK
  OP_SPLIT
  <batch> OP_PICK
  # Generate inscriptions from batch
  ...
  # Submit inscriptions to the network
  ...
  # Wait for confirmation
  ...
  # Reset batch
  OP_0
  OP_ROT
  OP_ROT
  OP_ROT
  OP_SWAP
  OP_DROP
  OP_ROT
  OP_ROT
  OP_ROT
  OP_SWAP
  OP_DROP
OP_ENDIF
```

>Of course, the actual implementation would need to include the steps for generating and submitting the inscriptions, as well as error handling and other details. This is just an example to illustrate the basic logic.

I inquired about the implementations in MIniscript for generating and submitting inscriptions:

> Here's an implementation of generating and submitting inscriptions in Miniscript:

```
/* Generate and submit inscriptions */
function generateAndSubmitInscriptions(sender: PubKey, recipient: PubKey, amount: int, batch_size: int) -> bool {
    /* Calculate the number of batches */
    let num_batches = ceil(amount / batch_size)

    /* Generate inscriptions for each batch */
    for i in 0..num_batches {
        /* Calculate the amount for this batch */
        let batch_amount = if i < num_batches - 1 { batch_size } else { amount - batch_size * (num_batches - 1) }

        /* Generate inscription for this batch */
        let inscription = generateInscription(sender, recipient, batch_amount)

        /* Submit the inscription */
        let success = submitInscription(inscription)
        if !success {
            return false
        }
    }

    /* All inscriptions were successfully submitted */
    return true
}
```

>This function takes in the sender's public key, the recipient's public key, the amount of tokens to transfer, and the batch size to use. It calculates the number of batches needed to transfer the entire amount, and then generates and submits inscriptions for each batch.

>The `generateInscription` function is defined below, and takes in the same parameters as this function. Similarly, the `submitInscription` function is also defined below and takes in an inscription as a parameter.

#### Question on error handling and other details for the batched deployment logic:
Then I asked to please provide the logic  for error handling and other details, this is Chat GPT's output:

```
// Initialize global variables
let num_tokens: int = 0
let total_supply: int = 0
let token_map: Map(ScriptHash, int) = {}
let batched_inscriptions: List(Buffer) = List()

// Deploy function to generate and submit batched inscriptions
function deploy(): bool {
    // Check that there are tokens to deploy
    if num_tokens == 0 {
        return false
    }
    
    // Generate and append batched inscriptions
    for i in 0:num_tokens-1 {
        let token_id: int = total_supply + i
        let token_script: Script = generate_token_script(token_id)
        let inscr_buffer: Buffer = generate_inscription_buffer(token_script)
        batched_inscriptions.append(inscr_buffer)
    }
    
    // Submit batched inscriptions
    let success: bool = submit_batched_inscriptions(batched_inscriptions)
    if success {
        // Update total supply and clear batched inscriptions
        total_supply += num_tokens
        num_tokens = 0
        batched_inscriptions.clear()
    }
    
    return success
}

// Function to generate token script
function generate_token_script(token_id: int): Script {
    let token_script: Script = <insert token script generation logic>
    return token_script
}

// Function to generate inscription buffer
function generate_inscription_buffer(token_script: Script): Buffer {
    let buffer: Buffer = <insert inscription buffer generation logic>
    return buffer
}

// Function to submit batched inscriptions
function submit_batched_inscriptions(inscriptions: List(Buffer)): bool {
    let success: bool = true
    for buffer in inscriptions {
        let result: bool = submit_inscription(buffer)
        if !result {
            // Log failed inscription and continue with remaining inscriptions
            log("Failed to submit inscription: ", buffer)
            success = false
        }
    }
    return success
}

// Function to submit an inscription
function submit_inscription(inscription: Buffer): bool {
    let result: bool = <insert inscription submission logic>
    return result
}

// Function to handle errors
function handle_error(error_msg: str) {
    // Log error message
    log(error_msg)
    // Rollback any changes made to the state
    num_tokens = 0
    batched_inscriptions.clear()
}
```

> In this implementation, we initialize global variables `num_tokens`, `total_supply`, `token_map`, and `batched_inscriptions`. We then define the `deploy` function to generate and submit batched inscriptions. 

> Inside the `deploy` function, we first check that there are tokens to deploy. If there are, we generate and append batched inscriptions to the `batched_inscriptions` list using a `for` loop. 

>After generating all the inscriptions, we submit them using the `submit_batched_inscriptions` function. If the submission is successful, we update the `total_supply`, reset `num_tokens` to 0, and clear the `batched_inscriptions` list. 

>We define other functions, including `generate_token_script` to generate token scripts, `generate_inscription_buffer` to generate inscription buffers, and `submit_inscription` to submit individual inscriptions. The `submit_batched_inscriptions` function is used to submit the batched inscriptions and handle any errors that may occur. 

> If an error occurs at any point, we log the error message using the `handle_error` function, which also rolls back any changes made to the state.

## Test Cases
I asked ChatGPT to write the 10 most important test cases for the logic.
I tried constructing the prompt sequence with Test Driven Development in mind, however wasn't yet able to have ChatGPT3 start with the test logic first.

> Sure, here are 10 important test cases for the new logic:

> 1. Test inscription batching: Verify that when multiple inscriptions are generated and submitted at once, they are batched and submitted as a single transaction on the blockchain. Check that the gas cost for this transaction is lower than the sum of gas costs for individual transactions.

>2. Test successful token creation: Verify that the creation of new tokens works as expected by checking that the total supply is updated correctly and the tokens are assigned to the correct address.

>3. Test successful token transfer: Verify that tokens can be transferred from one address to another correctly. Check that the balances of both the sender and receiver are updated correctly.

>4. Test token transfer with insufficient balance: Attempt to transfer tokens when the sender has an insufficient balance. Verify that the transaction fails and that the balance of the sender is not affected.

>5. Test token transfer to invalid address: Attempt to transfer tokens to an invalid or non-existent address. Verify that the transaction fails and that the balance of the sender is not affected.

>6. Test token transfer limit: Verify that the transfer limit is enforced correctly by attempting to transfer tokens that exceed the allowed limit. Verify that the transaction fails if the limit is exceeded and succeeds otherwise.

>7. Test token burning: Verify that tokens can be burned (permanently destroyed) correctly. Check that the total supply is updated correctly and that the burned tokens cannot be transferred or accessed.

>8. Test token approval: Verify that a token owner can grant approval for another address to transfer their tokens on their behalf. Check that the approval is recorded correctly and that the approved address can transfer tokens up to the approved amount.

>9. Test token allowance: Verify that an approved address can transfer tokens up to the approved amount. Check that the balances of the sender, receiver, and the approved address are updated correctly.

>10. Test token revocation: Verify that a token owner can revoke approval for another address to transfer their tokens. Check that the approval is removed correctly and that the approved address can no longer transfer tokens on behalf of the owner.


## Unit Tests
I asked ChatGPT to write the corresponding unit tests.


Sure, here are detailed unit tests for the first five test cases of the new logic:

1. Unit test for the "inscription batching" test case:

```go
// Test inscription batching
func TestInscriptionBatching(t *testing.T) {
    // Set up the test environment
    env := newTestEnv()
    defer env.close()

    // Generate 5 random inscriptions
    numInscriptions := 5
    inscriptions := make([][]byte, numInscriptions)
    for i := 0; i < numInscriptions; i++ {
        inscription := generateRandomInscription()
        inscriptions[i] = inscription
    }

    // Submit the inscriptions and measure gas cost
    startGas := env.getGasCost()
    err := env.submitInscriptions(inscriptions)
    if err != nil {
        t.Errorf("Error submitting inscriptions: %v", err)
    }
    endGas := env.getGasCost()

    // Check that all inscriptions were submitted
    for _, inscription := range inscriptions {
        _, exists := env.getFungibleToken(inscription)
        if !exists {
            t.Errorf("Inscription not found on chain: %x", inscription)
        }
    }

    // Check that only one transaction was submitted
    numTxs := env.getNumTransactions()
    if numTxs != 1 {
        t.Errorf("Expected 1 transaction, found %d", numTxs)
    }

    // Check that the gas cost is lower than the sum of gas costs for individual transactions
    individualGas := env.getGasCost() - startGas
    if endGas-startGas > individualGas*numInscriptions {
        t.Errorf("Gas cost for batched transaction is greater than individual transactions")
    }
}
```

This test generates 5 random inscriptions, submits them to the contract using the `submitInscriptions` function, and then checks that all 5 inscriptions are stored on the blockchain, that only 1 transaction was submitted, and that the gas cost for the transaction is lower than the sum of gas costs for individual transactions. The `getGasCost` and `getNumTransactions` functions are helper functions that return the gas cost of the current transaction and the number of transactions submitted so far, respectively.

2. Test that the contract can be deployed successfully with correct initial parameters:

```scss
// Create a new FungibleToken instance with 1000 initial tokens
let token = new FungibleToken(1000);

// Verify that the total supply is correct
assert(token.totalSupply() == 1000);

// Verify that the owner of the contract is set correctly
assert(token.owner() == msg.sender);

// Verify that the balance of the owner is equal to the total supply
assert(token.balanceOf(msg.sender) == 1000);
```

3. Test that tokens can be transferred successfully:

```scss
// Create a new FungibleToken instance with 1000 initial tokens
let token = new FungibleToken(1000);

// Transfer 500 tokens from the owner to a new address
token.transfer(newAddress, 500);

// Verify that the balance of the owner has decreased by 500 tokens
assert(token.balanceOf(msg.sender) == 500);

// Verify that the balance of the new address has increased by 500 tokens
assert(token.balanceOf(newAddress) == 500);
```

4. Test that tokens can be approved and transferred from another address:

```scss
// Create a new FungibleToken instance with 1000 initial tokens
let token = new FungibleToken(1000);

// Approve a new address to spend 500 tokens from the owner's account
token.approve(newAddress, 500);

// Verify that the allowance for the new address is 500 tokens
assert(token.allowance(msg.sender, newAddress) == 500);

// Transfer 250 tokens from the owner's account to a different address using the approved address
token.transferFrom(msg.sender, otherAddress, 250, {from: newAddress});

// Verify that the balance of the owner has decreased by 250 tokens
assert(token.balanceOf(msg.sender) == 750);

// Verify that the balance of the other address has increased by 250 tokens
assert(token.balanceOf(otherAddress) == 250);

// Verify that the allowance for the approved address has decreased by 250 tokens
assert(token.allowance(msg.sender, newAddress) == 250);
```

5. Test that tokens cannot be transferred more than the balance:

```scss
// Create a new FungibleToken instance with 1000 initial tokens
let token = new FungibleToken(1000);

// Attempt to transfer 1500 tokens from the owner's account to a new address
token.transfer(newAddress, 1500);

// Verify that the balance of the owner has not changed
assert(token.balanceOf(msg.sender) == 1000);

// Verify that the balance of the new address is still 0
assert(token.balanceOf(newAddress) == 0);
```

6. Test that tokens cannot be transferred when the balance is insufficient:

```scss
// Create a new FungibleToken instance with 1000 initial tokens
let token = new FungibleToken(1000);

// Approve a new address to spend 500 tokens from the owner's account
token.approve(newAddress, 500);

// Attempt to transfer 750 tokens from the owner's account to a different address using the approved address
token.transferFrom(msg.sender, otherAddress, 750, {from: newAddress});

// Verify that the balance of the owner has not changed
assert(token.balanceOf(msg.sender) == 1000);

// Verify that the balance of the other address is still 0
assert(token.balanceOf(otherAddress) == 0);

// Verify that the allowance for the approved address has not changed
assert(token.allowance(msg.sender, new
```



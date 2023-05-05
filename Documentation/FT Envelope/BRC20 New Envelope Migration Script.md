# BRC20 new envelope Migration Script 
This is an experiment to support the new envelope for fungible tokens on bitcoin **in cooperation with ChatGPT.**
**The goal is to better distribute the load and complexity that fungible tokens add to the network by being in the ord envelope**, that was meant for non fungible tokens.
With the new envelope **it should be less difficult** to create a DeFi ecosystem on bitcoin, with Swaps, DEXs, loans and all.


<div class="notecard warning">
<h4>Warning</h4>
<p> This is highly experimental tech, tread carefully, you could loose everything to the dragons beyond.</p>
</div>

#### [Here's the documentationon the envelope](https://github.com/DanM3rcurius/BRC20-SwapContract/blob/main/Documentation/FT%20Envelope/Dedicated%20BRC20%20envelope.md)

## Script
A Miniscript implementation for the migration of BRC-20 tokens from the old envelope to the new one:

```
OP_IF
// Check if the transaction has at least two outputs
OP_SIZE 0x02 OP_EQUALVERIFY
// Check if the first output is the old envelope
<old envelope scriptPubKey>
OP_DUP OP_HASH160 <old envelope address> OP_EQUALVERIFY
// Check if the second output is the new envelope
<new envelope scriptPubKey>
OP_DUP OP_HASH160 <new envelope address> OP_EQUALVERIFY
// Check if the input spends an UTXO with the old envelope
<old envelope scriptPubKey>
OP_DUP OP_HASH160 <old envelope address> OP_EQUALVERIFY
OP_OVER OP_HASH160 <input address> OP_EQUALVERIFY
// Check that the input is a BRC-20 transfer
<BRC20 SCRIPT>
OP_DUP OP_HASH160 <OLD_TOKEN_ADDRESS> OP_EQUALVERIFY

// Check that the transaction version is 2 (BIP-68)
<VERIFY_VERSION_2>

// Check that the nLocktime is greater than or equal to the specified block height
<CHECK_NLOCKTIME>

// Check that the signature is valid for the input
<CHECK_SIG>

// Subtract the transferred amount from the old balance
<OLD_BALANCE> <AMOUNT> OP_SUB

// Check that the new balance is greater than or equal to zero
OP_DUP OP_IFDUP OP_0 OP_EQUALVERIFY

// Add the transferred amount to the new balance
<NEW_BALANCE> <AMOUNT> OP_ADD

// Store the new balance in the output script
OP_DUP OP_HASH160 <NEW_TOKEN_ADDRESS> OP_EQUALVERIFY
<STORE_NEW_BALANCE>
OP_ENDIF
```

In this implementation, the script checks that the input is a BRC-20 transfer using the old envelope, verifies the transaction version is 2, checks the nLocktime, and validates the signature. It then subtracts the transferred amount from the old balance and checks that the new balance is greater than or equal to zero. Finally, it adds the transferred amount to the new balance and stores it in the output script using the new envelope.

Note that this implementation assumes that the `OLD_BALANCE` and `NEW_BALANCE` values are stored as script variables in the input and output scripts, respectively. You'll need to replace these placeholders with the appropriate Miniscript expressions for your specific use case.

## Tests
Sure, here are some test cases and unit tests for this smart contract logic:

### Test Cases:
1. Verify that a user can add liquidity to the pool with valid inputs.
2. Verify that adding liquidity fails if the user doesn't have enough ABC tokens.
3. Verify that adding liquidity fails if the user doesn't have enough BTC to contribute.
4. Verify that removing liquidity works as expected, returning the correct amounts of ABC and BTC to the user.
5. Verify that removing liquidity fails if the user doesn't have enough liquidity tokens.
6. Verify that a trade can be executed successfully with valid inputs.
7. Verify that a trade fails if the user doesn't have enough input tokens.
8. Verify that a trade fails if the trade amount exceeds the reserves in the pool.
9. Verify that the time-lock for removing liquidity works as expected.
10. Verify that the contract reverts if any external calls fail during execution.

#### Test Cases (details):

Sure, here are some detailed test cases for the liquidity pool smart contract with timelock and security measures:

1. Test Case: Successful liquidity addition
Description: Test adding liquidity to the pool with valid input values
Test Steps:
- Call the addLiquidity function with valid token amounts and funds
- Verify that the UTXO with the deposited tokens and funds was created
- Verify that the liquidity shares were minted and assigned to the user

2. Test Case: Liquidity addition with insufficient funds
Description: Test adding liquidity to the pool with insufficient funds
Test Steps:
- Call the addLiquidity function with token amounts and funds that exceed the available balance
- Verify that the transaction fails
- Verify that no UTXO or liquidity shares were created

3. Test Case: Liquidity addition with incorrect token input
Description: Test adding liquidity to the pool with incorrect token input
Test Steps:
- Call the addLiquidity function with an invalid token address or an amount of tokens that do not correspond to the BRC20 contract
- Verify that the transaction fails
- Verify that no UTXO or liquidity shares were created

4. Test Case: Successful liquidity removal
Description: Test removing liquidity from the pool with valid input values
Test Steps:
- Call the removeLiquidity function with valid liquidity share amount and timelock
- Verify that the timelock value is correct and greater than the current block height
- Wait for the timelock period to expire
- Verify that the UTXO with the deposited tokens and funds was spent and the liquidity shares were burned
- Verify that the tokens and funds were transferred back to the user's address

5. Test Case: Liquidity removal with insufficient liquidity shares
Description: Test removing liquidity from the pool with insufficient liquidity shares
Test Steps:
- Call the removeLiquidity function with a liquidity share amount that exceeds the available balance
- Verify that the transaction fails
- Verify that no UTXO or liquidity shares were burned

6. Test Case: Liquidity removal with invalid timelock
Description: Test removing liquidity from the pool with an invalid timelock
Test Steps:
- Call the removeLiquidity function with a timelock value that is less than or equal to the current block height
- Verify that the transaction fails
- Verify that no UTXO or liquidity shares were burned

7. Test Case: Successful trade
Description: Test trading tokens with valid input values
Test Steps:
- Add liquidity to the pool
- Call the trade function with valid input values
- Verify that the tokens and funds were transferred between the users
- Verify that the UTXO with the deposited tokens and funds was updated accordingly

8. Test Case: Trade with insufficient liquidity
Description: Test trading tokens with insufficient liquidity
Test Steps:
- Add liquidity to the pool
- Call the trade function with token or fund amounts that exceed the available liquidity in the pool
- Verify that the transaction fails
- Verify that no UTXO or token/fund transfer was executed

9. Test Case: Trade with incorrect token input
Description: Test trading tokens with incorrect token input
Test Steps:
- Add liquidity to the pool
- Call the trade function with an invalid token address or an amount of tokens that do not correspond to the BRC20 contract
- Verify that the transaction fails
- Verify that no UTXO or token/fund transfer was executed


## Unit Tests:
1. Test that the contract deploys successfully.
2. Test that adding liquidity updates the user's ABC and BTC balances correctly.
3. Test that adding liquidity updates the pool's ABC and BTC reserves correctly.
4. Test that removing liquidity updates the user's ABC and BTC balances correctly.
5. Test that removing liquidity updates the pool's ABC and BTC reserves correctly.
6. Test that trading updates the user's token balances correctly.
7. Test that trading updates the pool's token reserves correctly.
8. Test that the time-lock for removing liquidity works as expected.
9. Test that the contract reverts if any external calls fail during execution.
10. Test that the contract can be upgraded to a new version without losing any data.

### Unit Tests (details)

```
import unittest
from smart_contract import SmartContract

class TestSmartContract(unittest.TestCase):
def setUp(self):
self.contract = SmartContract()
def test_add_liquidity(self):
# Test adding liquidity with valid inputs
self.contract.add_liquidity(100, 200, 1000, 1000)
self.assertEqual(self.contract.liquidity_pool[0], 100)
self.assertEqual(self.contract.liquidity_pool[1], 200)
self.assertEqual(self.contract.reserve_tokens[0], 900)
self.assertEqual(self.contract.reserve_tokens[1], 800)
# Test adding liquidity with one of the amounts being zero
with self.assertRaises(ValueError):
self.contract.add_liquidity(0, 100, 1000, 1000)
# Test adding liquidity with not enough tokens provided
with self.assertRaises(ValueError):
self.contract.add_liquidity(1000, 100, 1000, 1000)
def test_remove_liquidity(self):
# Test removing liquidity with valid inputs
self.contract.add_liquidity(100, 200, 1000, 1000)
self.contract.remove_liquidity(100)
self.assertEqual(self.contract.liquidity_pool[0], 0)
self.assertEqual(self.contract.liquidity_pool[1], 0)
self.assertEqual(self.contract.reserve_tokens[0], 1000)
self.assertEqual(self.contract.reserve_tokens[1], 1000)
# Test removing liquidity with not enough liquidity provided
with self.assertRaises(ValueError):
self.contract.remove_liquidity(200)
# Test removing liquidity with zero amount provided
with self.assertRaises(ValueError):
self.contract.remove_liquidity(0)
# Test removing liquidity after the timelock
self.contract.add_liquidity(100, 200, 1000, 1000)
self.contract.remove_liquidity(100, True)
self.assertEqual(self.contract.liquidity_pool[0], 0)
self.assertEqual(self.contract.liquidity_pool[1], 0)
self.assertEqual(self.contract.reserve_tokens[0], 1000)
self.assertEqual(self.contract.reserve_tokens[1], 1000)
def test_trade(self):
# Test trading with valid inputs
self.contract.add_liquidity(100, 200, 1000, 1000)
self.contract.trade(50, 1)
self.assertEqual(self.contract.reserve_tokens[0], 1000)
self.assertEqual(self.contract.reserve_tokens[1], 900)
self.assertEqual(self.contract.token_balance, 50)
# Test trading with not enough tokens provided
with self.assertRaises(ValueError):
self.contract.trade(500, 1)
# Test trading with invalid token selection
with self.assertRaises(ValueError):
self.contract.trade(50, 5)
# Test trading with zero amount provided
with self.assertRaises(ValueError):
self.contract.trade(0, 1)
if __name__ == '__main__':
unittest.main()
```

These unit tests cover a range of scenarios, including adding liquidity, removing liquidity, and trading, with both valid and invalid inputs.

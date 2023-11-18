# Solidity Interview Questions

These are responses to [RareSkills](https://www.rareskills.io/)' [_140 Solidity Interview Questions_](https://www.rareskills.io/post/solidity-interview-questions).

## Easy
> 1. What is the difference between `private`, `internal`, `public`, and `external` functions?

`external` functions can only be called externally to the contract in which the function is declared. `public` functions can be called from externally in the contract, as well as in the current contract, and any inheriting contracts. `internal` contracts can be called form within the current contract as well as inheriting contracts, but nothing else. `private` functions can only be called from internally in the current contract. Not externally nor in any inheriting contracts. ([Source](https://docs.soliditylang.org/en/v0.8.23/contracts.html#function-visibility))

> 2. Approximately, how large can a smart contract be?

A smart contract can be 24.576 kb. ([Source](https://ethereum.org/en/developers/tutorials/downsizing-contracts-to-fight-the-contract-size-limit/))

> 3. What is the difference between `CREATE` and `CREATE2`?

`CREATE` creates a new contract using the provided initialization code, and the contract code is the return value. The created contract's address is derived is the last 20 bytes of the keccack-256 hash of the RLP encoding of the sender's address along with its nonce. For EOAs, the nonce is its amount of  transactions; for a contract, it is the amount of contracts it has created.

On the other hand, `CREATE2` creates a new contract deterministically. It works similar to `CREATE`, but with the usage of a _salt_ and not the usage of the nonce. The salt is an arbitrary `bytes32` value provided by the user, which allows the creation of a deterministic address. (Sources: [one](https://www.evm.codes/#f0?fork=shanghai), [two](https://docs.soliditylang.org/en/v0.8.23/control-structures.html#creating-contracts-via-new), [three](https://docs.openzeppelin.com/cli/2.8/deploying-with-create2#creating_a_smart_contract))

> 4. What major change with arithmetic happened with Solidity 0.8.0?

In Solidity `0.8.0`, the native feature of underflow/overflow protection was introduced. Though, to bypass this, you can include `unchecked { ... }` to your code. ([Source](https://docs.soliditylang.org/en/v0.8.23/080-breaking-changes.html#silent-changes-of-the-semantics))

> 5. What special CALL is required for proxies to work?

The special "call" opcode that is needed for proxies to work is `DELEGATECALL`. The `DELEGATE` call allows a proxy to receive a _call_ and _delegate_ it to an implementation contract. On a technical level, how this works, this call allows the function call to execution the logic of the implementation contract in the context of the proxy contract. (Sources: [one](https://www.evm.codes/#f0?fork=shanghai), [two](https://blog.openzeppelin.com/blog/proxy-patterns))

> 6. Prior to EIP-1559, how do you calculate the dollar cost of an Ethereum transaction?

EVM opcodes cost gas to execute, and to execute a transaction on-chain, a user would have to pay for all of the individual opcodes that would be executed in their transaction. The sum of all the gas required by the opcodes would be have a use has to pay in gas. To find the dollar cost of a transaction, you would then multiply that amount of gas fees by the price of ETH at the time of execution. ([Source](https://www.blocknative.com/blog/eth-gas-101))

> 7. What are the challenges of creating a random number on the blockchain?

All of the transactions on-chain are generated deterministically, therefore, there is no native source of randomness on-chain. Because of that, it is very challenging to use any on-chain source of randomness. ([Source](https://github.com/ethereumbook/ethereumbook/blob/develop/09smart-contracts-security.asciidoc#entropy-illusion))

> 8. What is the difference between a Dutch Auction and an English Auction?

An English auction is an open-outcry ascending dynamic auction. Different from other auction systems in its most essential feature: the public bidding process can transmit information to bidders in real-time because it can potentially realize the sharing of private information. By introducing the common value factor, the English auction has a revenue advantage: each bidder's private information about the common value is valuable information to the other bidders, and this information is disclosed during the public bidding process.

In contracts, a Dutch auction is an auction in which the auctioneer begins with a high asking price in the case of selling, and lowers it until some participant accepts the price, or it reaches a predetermined reserve price. (Sources: [one](https://en.wikipedia.org/wiki/English_auction), [two](https://en.wikipedia.org/wiki/Dutch_auction))

> 9. What is the difference between transfer and transferFrom in ERC20?

`transfer` allows the owner of said ERC20 tokens to be transferred, while `transferFrom` allows both the owner _and_ an account to which the owner has authorized (via `approve`) to do so as well. This commonly used to authorize DeFi apps to transfer tokens on an owner's behalf. ([Source](https://eips.ethereum.org/EIPS/eip-20))

> 10. Which is better to use for an address allowlist: a mapping or an array? Why?

For gas efficiency reasons, that would be a `mapping`. That is because if it were to be implemented using an array, if at some point, the array needed to be iterated upon, that would utilized a lot of gas. As opposed to a mapping, which would require no iterating. ([Source](https://docs.alchemy.com/docs/how-to-create-an-on-chain-nft-allowlist))

> 11. Why shouldn’t tx.origin be used for authentication?

`tx.origin` shouldn't be used for origin because it can fall susceptible to "man in the middle" attacks. That is because `tx.origin` will equal the EOA that initiated the transaction. For example, an app that uses `tx.origin` for authentication could be exploited by an entity who gets a legitimate account to be the `tx.origin`, then add some logic in the "middle" that is exploitative in nature. (Sources: [one](https://github.com/ethereumbook/ethereumbook/blob/develop/09smart-contracts-security.asciidoc#txorigin-authentication), [two](https://secureum.substack.com/p/security-pitfalls-and-best-practices-101))

> 12. What hash function does Ethereum primarily use?

Ethereum primarily uses `keccak256` for hashing. ([Source](https://github.com/ethereumbook/ethereumbook/blob/develop/04keys-addresses.asciidoc#ethereums-cryptographic-hash-function-keccak-256))

> 13. How much is 1 gwei of Ether?

1 gwei of Ether is 10^-9 ([Source](https://etherscan.io/unitconverter))

> 14. How much is 1 wei of Ether?

1 wei of Ether is 10^-18 ([Source](https://etherscan.io/unitconverter))

> 15. What is the difference between assert and require?

`assert` should only be used to test for internal errors, and to check invariants. Also, `assert` creates an error of type `Panic(uint256)`.

`require` should be used to ensure valid conditions that cannot be detected until execution time. This includes conditions on inputs or return values from calls to external contracts. Also, `require` function either creates an error without any data or an error of type `Error(string)`. ([Source](https://docs.soliditylang.org/en/v0.8.23/control-structures.html#error-handling-assert-require-revert-and-exceptions))

> 16. What is a flash loan?

A flash loan is a type of loan where a user borrows assets with no upfront collateral and returns the borrowed assets within the same blockchain transaction. ([Source](https://chain.link/education-hub/flash-loans))

> 17. What is the check-effects pattern?

The _Checks-Effects-Interactions_ patterns is such that in a function logic, checks are carried out first. Only then should state altering modifications to the current contract should be executed. Last, would be any interaction with external contracts. ([Source](https://docs.soliditylang.org/en/v0.8.23/security-considerations.html#use-the-checks-effects-interactions-pattern))

> 18. What is the minimum amount of Ether required to run a solo staking node?

To run a solo staking node, you need 32 ETH. ([Source](https://www.blocknative.com/blog/ethereum-validator-staking-guide))

> 19. What is the difference between fallback and receive?

`receive` is the function that is executed when a contract is called with empty `calldata` and is executed on plain Ether transfers. Additionally, it can only rely on `2300` gas being available.

`fallback` is the function that is executed if none of the other functions match the provided signature _or_ if no data was supplied and there is no `receive` function. Alos, it always receive data but can only receive Ether if it is marked `payable`. ([Source](https://docs.soliditylang.org/en/v0.8.23/contracts.html#special-functions))

> 20. What is reentrancy?

Reentrancy is when a contract is invoked, and as part of its logic, it calls a seperate contract. The seperate contract can then, as part of _its_ logic, call back into the initial (first) contract. And depending on the logic of the first contract, could be exploited by the one reentering. ([Source](https://www.alchemy.com/overviews/reentrancy-attack-solidity))

> 21. As of the Shanghai upgrade, what is the gas limit per block?

As of the Shanghai upgrade, and today 18 November 2023, the block gas limit is ~30 million. ([Source](https://etherscan.io/chart/gaslimit))

> 22. What prevents infinite loops from running forever?

Each opcode that is carried out as part of a transaction execution consumes some gas, therefore, it is impossible for infinite loops from running forever because the transaction would either reach the user's gas limit, and if not, the block's gas limit. ([Source](https://github.com/ethereumbook/ethereumbook/blob/develop/01what-is.asciidoc#implications-of-turing-completeness))

> 23. What is the difference between tx.origin and msg.sender?

`msg.sender` is the sender of the current call, while `tx.origin` is the account that initiated the transaction. ([Source](https://docs.soliditylang.org/en/v0.8.23/cheatsheet.html#block-and-transaction-properties))

> 24. How do you send Ether to a contract that does not have payable functions, or a receive or fallback?

A contract without a `receive` Ether function can receive Ether as a recipient of a _coinbase transaction_ or as a destination of a `SELFDESTRUCT`. ([Source](https://docs.soliditylang.org/en/v0.8.23/contracts.html#receive-ether-function))

> 25. What is the difference between view and pure?

`view` functions promise not to modify the state. `pure` functions promise not to read from or modify the state. ([Source](https://docs.soliditylang.org/en/v0.8.23/contracts.html#state-mutability))

> 26. What is the difference between transferFrom and safeTransferFrom in ERC721?

With `safeTransferFrom`, when the transfer is complete, it checks if the recipient is a smart contract (code size > 0). If so, it calls `onERC721Received` on the recipient and throws if the return value is not `bytes4(keccak256("onERC721Received(address,address,uint256,bytes)"))`. This is a check to confirm that it is equipped to receive NFTs. `transferFrom` does not have this check. ([Source](https://eips.ethereum.org/EIPS/eip-721))

> 27. How can an ERC1155 token be made into a non-fungible token?

With `ERC1155`, each `tokenId` can have its own `supply`. Therefore, to have a non-fungible token (NFT), it would need to have a `supply` of one. ([Source](https://eips.ethereum.org/EIPS/eip-1155))

> 28. What is access control and why is it important?

Smart contracts are public, anyone can see and call the code. So it becomes fundamental that contracts implement secure and reliable access controls. These controls must be robust enough to prevent unauthorized accounts from executing sensitive functions, while at the same time, flexible enough to mutate and evolve as new features are added. ([Source](https://blog.openzeppelin.com/blog/workshop-recap-secure-development-workshop-2))

> 29. What does a modifier do?

Modifiers can be used to change the behavior of functions in a declarative way. For example, you can use a modifier to automatically check a condition prior to executing the function. ([Source](https://docs.soliditylang.org/en/v0.8.23/contracts.html#function-modifiers))

> 30. What is the largest value a uint256 can store?

The `uint256` max value can be obtained with `type(uint256).max;` which is `115792089237316195423570985008687907853269984665640564039457584007913129639935` or `2^256-1`. ([Source](https://www.rareskills.io/post/uint-max-value-solidity))

> 31. What is variable and fixed interest rate?

A variable interest rate can fluctuate based on changes to index rates, like the prime rate, a fixed interest rate typically doesn’t change throughout the loan term. ([Source](https://www.capitalone.com/learn-grow/money-management/fixed-vs-variable-apr/))
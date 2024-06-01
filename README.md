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


## Medium


> 32. What is the difference between transfer and send? Why should they not be used?

While both `transfer` and `send` send a given amount of Wei to the intended recipient, and forward a 2300 gas stipend, they differ in that `transfer` reverts on failure and `send` returns `false` on failure.

However, it is recommend that they both not be used anymore, and instead contracts opt to use `call`. That is because gas costs can change over time such as the case of a contract's `fallback` now use more than the previous 2300 gas. That is the same amount that `transfer` and `send` forward. (Sources: [one](https://docs.soliditylang.org/en/v0.8.23/units-and-global-variables.html#members-of-address-types), [two](https://consensys.io/diligence/blog/2019/09/stop-using-soliditys-transfer-now/))

> 33. How do you write a gas-efficient for loop in Solidity?

Some examples of how to write a more gas-efficient `for` loop are:
- Cache the length of the array
- increment the iterator (e.g. `i`) by using `++i`
- If using solidity prior to [0.8.22](https://soliditylang.org/blog/2023/10/25/solidity-0.8.22-release-announcement/), you could wrap the act of incrementing the iterator in `unchecked { ... }`

([Source](https://solidity-by-example.org/gas-golf/))

> 34. What is a storage collision in a proxy contract?

For example, ff the proxy contract has a variable stored at a specific storage slot, and the implementation contract has a _different_ variable stored in the same slot inside of itself, that could cause a storage collision. Because when the implementation is executing the logic, if it were to reference that variable in the same slot, because it is executing in the context of the proxy, and can alter the proxy's variable unexpectedly. ([Source](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies#unstructured-storage-proxies))

> 35. What is the difference between abi.encode and abi.encodePacked?

`abi.encode` will ABI-encode the given arguments, while padding them to 32 bytes, while `abi.encodePacked` does **NOT** pad the arguments to 32 bytes. ([Source](https://docs.soliditylang.org/en/v0.8.23/units-and-global-variables.html#abi-encoding-and-decoding-functions))

> 36. uint8, uint32, uint64, uint128, uint256 are all valid uint sizes. Are there others?

Besides the `uint16` that was missed, there are numerous more `uint` sizes. ([Source](https://docs.soliditylang.org/en/v0.8.23/grammar.html#a4.SolidityLexer.UnsignedIntegerType))

> 37. What changed with block.timestamp before and after proof of stake?

`block.timestamp` is the current block timestamp as seconds since unix epoch. However, after _The Merge_ and Ethereum's transition to Proof of Stake, the time for slots, which have blocks inside of them, increases in 12 second increments automatically. This make it much less suceptible to be manipulated by validators, as opposed to miner in Proof of Work. ([Source](https://www.blocknative.com/blog/ethereum-merge-proof-of-stake))

> 38. What is frontrunning?

Frontrunning is when an entity copies a transaction from the mempool and bribes the block producer with a higher gas fee to get their transaction included ahead of the original transaction. ([Source](https://www.alchemy.com/overviews/what-is-mev#:~:text=Frontrunning%20is%20when%20an%20entity,ahead%20of%20the%20original%20transaction.))

> 39. What is a commit-reveal scheme and when would you use it?

A _commit-reveal scheme_ is a way to circumvent the ability for someone's data to be copied by someone. For example, if you have a game where people are attempting to submit answers, the first players would be at a disadvantage because the other players can see the outcome of their submissions. A commit-reveal scheme could help in this scenario.

How it would do so, if you would have the players submit a _hash_ of their responses. Then after everyone has submitted, people could then provide their actual responses to be hashed, then have their hashes be compared with their initially supplied hashes to prove that they didn't change their answer after the fact. ([Source](https://www.gitcoin.co/blog/commit-reveal-scheme-on-ethereum))

> 40. Under what circumstances could abi.encodePacked create a vulnerability?

`abi.encodePacked` can result in hash collisions when passing more than one dynamic data type because it does not pad the arguments like `abi.encode` does. (Sources: [one](https://swcregistry.io/docs/SWC-133/), [two](https://x.com/bytes032/status/1613456938104233984?s=20))

> 41. How does Ethereum determine the BASEFEE in EIP-1559?

The Base Fee is determined by the Ethereum network rather than being set by end-users looking to transact or miners seeking to validate transactions. The Base Fee targets 50% full blocks and is based upon the contents of the most recent confirmed block. Depending on how full that new block is, the Base Fee is automatically increased or decreased. ([Source](https://www.blocknative.com/blog/eip-1559-fees))

> 42. What is the difference between a cold read and a warm read?

A cold read is when you read a storage variable for the first time, and a warm read is when you read a storage variable that has been read already in the same transaction. For example, if you're reading a storage variable for the first time, that will be a cold read and be expensive relative to a warm read. However, if you then read that variable again in the same transaction, it will now be a warm read, it still being in memory, and cost less gas. ([Source](https://eips.ethereum.org/EIPS/eip-2929#storage-read-changes))

> 43. How does an AMM price assets?

One way that AMMs price assets are through the usage of the _constant product formula_, `x * y = k`. As implied by the name, the product, `k`, will not change relative to its reserve tokens (`x` & `y`) as the two reserve tokens are swapped for one another. This has the effect that large trades, relative to the balance of the pool, will affect the reserves price. ([Source](https://docs.uniswap.org/contracts/v2/concepts/protocol-overview/how-uniswap-works))

> 44. What is a function selector clash in a proxy and how does it happen?

A function selector clash is when the function selector in the data passed into the proxy matches one of the functions implemented in the proxy, and thus would not invoke the proxy's fallback. Seeing as how the fallback houses the `DELEGATECALL` to the implementation contract, such a clash would prevent calling the intended implementation contracts. ([Source](https://medium.com/nomic-foundation-blog/malicious-backdoors-in-ethereum-proxies-62629adf3357))

> 45. What is the effect on gas of making a function payable?

If you make functions payable, this would be gas efficient because the compiler will not need to confirm `CALLVALUE` is zero. ([Source](https://www.rareskills.io/post/gas-optimization#viewer-18pd1))

> 46. What is a signature replay attack?

A signature replay attack is when a transaction is executed after already been executed once. This is due to reusing the digital signature of the already-used transaction. A specific type of signature replay attack, the _cross-chain_ replay attack could be prevent by proper use of a chain id. A _same-chain_ replay attack could be prevented by the usage of the account's nonce. (Sources: [one](https://slowmist.medium.com/intro-to-smart-contract-security-audit-signature-replay-b71c23910629), [two](https://solidity-by-example.org/hacks/signature-replay/))

> 47. What is gas griefing?

Gas griefing is when a contract that makes an external call to another contract does not check the return state of the externally called contract. Thus, an exploiter could submit transactions that do not provide sufficient gas for the external contract call. Then, since the return is not checked, the called contract does not know the external called did not actually compute. ([Source](https://scsfg.io/hackers/griefing/))

> 48. How would you design a game of rock-paper-scissors in a smart contract such that players cannot cheat?

A simple implementation that could be done for _rock-paper-scissors_ is to implement a game using the _commit-reveal_ scheme. With that in place, the players would not know what other players submitted until after all players have submitted. (See answer 39)

> 49. What is the free memory pointer and where is it stored?

The free memory pointer is simply a pointer to the location where free memory starts. It ensures smart contracts keep track of which memory locations have been written to and which haven’t. The free memory pointer is located in memory at byte `0x40`. ([Source](https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy-d6b#%C2%A7free-memory-pointer))

> 50. What function modifiers are valid for interfaces?

All declared functions must be `external` in the interface, even if they are `public` in the contract.. ([Source](https://docs.soliditylang.org/en/stable/contracts.html#interfaces))

> 51. What is the difference between memory and calldata in a function argument?

memory and calldata are both temporary data storage locations in Solidity, but they have important differences. Memory is used to hold temporary variables during function execution, while Calldata is used to hold function arguments passed in from an external caller. Calldata is read-only and cannot be modified by the function, while Memory can be modified. If you need to modify function arguments that are stored in calldata, you must first copy them into memory.
([Source](https://docs.alchemy.com/docs/what-is-the-difference-between-memory-and-calldata-in-solidity))

> 52. Describe the three types of storage gas costs.

The three types of storage are storage, memory, and the stack. Storage is by far the most expensive, because it cost a lot of gas to store data in the chain long term. Memory is the second most expensive one and costly the larger it grows (it scales quadratically). Lastly, stack is the least expensive of the three. ([Source](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#storage-memory-and-the-stack))

> 53. Why shouldn’t upgradeable contracts use the constructor?

In Solidity, code that is inside a constructor or part of a global variable declaration is not part of a deployed contract’s runtime bytecode. This code is executed only once, when the contract instance is deployed. As a consequence of this, the code within a logic contract’s constructor will never be executed in the context of the proxy’s state. To rephrase, proxies are completely oblivious to the existence of constructors. It’s simply as if they weren’t there for the proxy.
([Source](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies#the-constructor-caveat))

> 54. What is the difference between UUPS and the Transparent Upgradeable Proxy pattern?

The transparent proxy pattern is where function calls initiated by the end user (caller) are always routed to the logic contract rather than the proxy contract. However, if the caller is an admin of the proxy, the proxy will know to invoke its own administrative function.

The Universal Upgradable Proxy Standard (UUPS) was proposed in `EIP1822` as a way to create a standard for proxy contracts that have universal compatibility with all contracts. It overcomes the issue of proxy function selector clashes. In UUPS, the upgrades are handled by the logic contract—specifically the “proxiable” smart contract that the logic contract inherits from.

([Source](https://blog.chain.link/upgradable-smart-contracts/))

> 55. If a contract delegatecalls an empty address or an implementation that was previously self-destructed, what happens? What if it is a regular call instead of a delegatecall?

When Contract A attempts to make a `DELEGATECALL` (or regular `CALL`) to Contract B, and Contract B has previously executed a self-destruct, the delegatecall will return a success. That is because the two opcodes return success as `true` for calls to accounts with no code. ([Source](https://www.evm.codes/?fork=shanghai))

> 56. What danger do ERC777 tokens pose?

They pose a danger of potential reentrancy. That is because of the implementation of the optional _pre-transfer hooks_. This hook hands control flow over to the sender, which can lead to the exploitation of reentrancy vulnerabilities. ([Source](https://medium.com/immunefi/the-potential-impact-of-erc-777-tokens-on-defi-protocols-51cdb07be733))

> 57. According to the solidity style guide, how should functions be ordered?

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private

Within a grouping, place the `view` and `pure` functions last.

([Source](https://docs.soliditylang.org/en/stable/style-guide.html#order-of-functions))

> 58. According to the solidity style guide, how should function modifiers be ordered?

The modifier order for a function should be:
- Visibility
- Mutability
- Virtual
- Override
- Custom modifiers

([Source](https://docs.soliditylang.org/en/stable/style-guide.html#function-declaration))

> 59. What is a bonding curve?

A bonding curve is a mathematical function that connects the supply of a digital asset with its value. By implementing a bonding curve formula, the price of a newly issued token changes in response to changes in the token’s supply (as it is bought or sold) (Sources: [one](https://www.linumlabs.com/articles/bonding-curves-the-what-why-and-shapes-behind-it), [two](https://hackernoon.com/what-is-a-bonding-curve-and-how-does-it-affect-token-price))

> 60. How does safeMint differ from mint in the OpenZeppelin ERC721 implementation?

`mint` mints `tokenId` and transfers it to `to`. `safeMint` mints `tokenId`, transfers it to `to` and checks for `to` acceptance, specifically the implementation of `IERC721Receiver.onERC721Received`. (Sources: [one](https://docs.openzeppelin.com/contracts/5.x/api/token/erc721#ERC721), [two](https://docs.openzeppelin.com/contracts/5.x/api/token/erc721#IERC721Receiver-onERC721Received-address-address-uint256-bytes-))

> 61. What keywords are provided in Solidity to measure time?

Suffixes like `seconds`, `minutes`, `hours`, `days` and `weeks` after literal numbers can be used to specify units of time where seconds are the base unit. ([Source](https://docs.soliditylang.org/en/stable/units-and-global-variables.html#time-units))

> 62. What is a sandwich attack?

A sandwich attack consists of two rapid swaps surrounding a victim’s trade. It begins with an innocent trader sending their order to the public mempool, at which point the bot — always searching — spots its opportunity. 

The attack begins with a frontrun, followed up by an attack on the backend of the selected trade, hence the term “sandwich.” ([Source](https://blockworks.co/news/sandwich-attack-mev-ethereum))

> 63. If a delegatecall is made to a function that reverts, what does the delegatecall do?

`success` returns `0` if the sub context reverted. ([Source](https://www.evm.codes/?fork=shanghai))

> 64. What is a gas efficient alternative to multiplying and dividing by a multiple of two?

In Solidity, it is often more gas efficient to multiply or divide numbers that are powers of two by shifting their bits, rather than using the multiplication or division operators.
([Source](https://www.rareskills.io/post/gas-optimization#viewer-cvebl))

> 65. How large a uint can be packed with an address in one slot?

From the idea of "slot packing", multiple variables can be packed into a single 32-byte storage slot if combined they take up 32 bytes or less. Seeing as how an address is 20 bytes, that leaves 12 bytes (or 96 bits) left for a `uint96` uint to be packed along with it. ([Source](https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy-3ea))

> 66. Which operations give a partial refund of gas?

Setting a storage value to zero refunds you some gas as that key value no longer needs to be stored by the nodes on the network. (Sources: [one](https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy-3ea), [two]())

> 67. What is ERC165 used for?

ERC-165 is used to confirm a specific contract supports a specific interface. This is to ensure a specific function is found in the contract before attempting to invoke it. (Sources: [one](https://medium.com/@chiqing/ethereum-standard-erc165-explained-63b54ca0d273), [two](https://docs.openzeppelin.com/contracts/5.x/utilities#introspection))

> 68. If a proxy makes a delegatecall to A, and A does address(this).balance, whose balance is returned, the proxy's or A?

If a proxy makes a `DELEGATECALL` to contract _A_, and _A_ uses `address(this).balance`, the balance of the **proxy** is returned. ([Source](https://www.evm.codes/?fork=shanghai))

> 69. What is a slippage parameter useful for?

Slippage tolerances establish a margin of change acceptable to the user beyond price impact. As long as the execution price is within the slippage range, e.g., %1, the transaction will be executed. If the execution price ends up outside of the accepted slippage range, the transaction will fail, and the swap will not occur. ([Source](https://docs.uniswap.org/concepts/protocol/swaps#slippage))

> 70. What does ERC721A do to reduce mint costs? What is the tradeoff?

Azuki, as part of their implementation of `ERC-721A`, implemented three optimizations. One, they removed the "redundant" storage of a token's metadata and other storge, partyly because their tokens are sequentially numbered. Two, updating the owner’s balance once per batch mint request, instead of per minted NFT. Three, updating the owner data once per batch mint request, instead of per minted NFT. ([Source](https://www.azuki.com/erc721a))

> 71. Why doesn't Solidity support floating point arithmetic?

Solidity doesn't support floating point arithmetic because it itself doesn't support floating-point or fixed-point data types. This can cause unexpected behavior in the smart contract functionality, such as rounding errors or arithmetic overflow and underflow conditions that can manifest itself through a huge loss of funds. ([Source](https://docs.compound.finance/interest-rates/#get-utilization))

> 72. What is TWAP?

_TWAP_, or time weighted average price, is constructed by reading the cumulative price from an ERC20 token pair at the beginning and at the end of the desired interval. The difference in this cumulative price can then be divided by the length of the interval to create a TWAP for that period. ([Source](https://docs.uniswap.org/contracts/v2/concepts/core-concepts/oracles))

> 73. How does Compound Finance calculate utilization?

The formula for producing the protocol utilization of the base asset is:
```
Utilization = TotalBorrows / TotalSupply
```

([Source](https://docs.compound.finance/interest-rates/#get-utilization))

> 74. If a delegatecall is made to a function that reads from an immutable variable, what will the value be?

The value will be the value of the `immutable` variable since its value is in the implementation contract's bytecode, rather in a storage slot. ([Source](https://docs.soliditylang.org/en/stable/contracts.html#constant-and-immutable-state-variables))


## Hard


> 75. How does fixed point arithmetic represent numbers?

Fixed-point airthmetic represents numbers by allocating a certain amount of bits to the fractional part. For example, Uniswap, in V3, utilizes Q64.95, in `Q` notation, to store their price (there is additional details, but this is enough for illustrative purposes). Their `sqrtPriceX96` is stored as a `uint160` and with `Q64.96` means that the first 64 bits store the integer, and the remaining 96 bits store the fractional part. It is a way to represent floating point numbers in Solidity, since it does not natively support floating point numbers. (Sources: [one](https://blog.uniswap.org/uniswap-v3-math-primer), [two](https://en.wikipedia.org/wiki/Q_(number_format)))

> 76. What is an ERC20 approval frontrunning attack?

An `ERC20` approval frontrunning attack is when address `A` grants address `B` to `transferFrom` `N` amount of tokens via the `approve` function. Then some time later, address `A` sends another `approve` transaction for address `B` to be a new updated amount, `M`. `B` could then see the transaction, before it is included in a block, and send a fronrunning `transferFrom` for `N` amount. Then once the `approve` for `M` is included in a block, could `transferFrom` for `M`. Effectly, via this frontrunning, address `B` would've been able to send both `N` and `M` amount of `ERC20` tokens. ([Source](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.m9fhqynw2xvt))

> 77. What opcode accomplishes address(this).balance?

The EVM opcode that accomplishes `address(this).balance` is `SELFBALANCE`, which per [evm.codes](https://www.evm.codes/), "[g]et balance of currently executing account". [Source](https://www.ethervm.io/#47)

> 78. How many arguments can a solidity event have?

For unindexed arguments, you are limited by the stack limit. For indexed arguments, you are limited to three arguments, or four if you declare an `anonymous` event. ([Source](https://www.rareskills.io/post/ethereum-events))

> 79. What is an anonymous Solidity event?

Anonymous events are those that have no `selector`, but are allowed up to four `indexed` arguments. ([Source](https://www.rareskills.io/post/ethereum-events))

> 80. Under what circumstances can a function receive a mapping as an argument?

They can be used for parameters in library functions. Additionally, they cannot be used as parameters or return parameters of contract functions that are publicly visible. (Sources: [one](https://docs.soliditylang.org/en/v0.8.16/contracts.html#function-parameters), [two](https://docs.soliditylang.org/en/latest/types.html#mapping-types))

> 81. What is an inflation attack in ERC4626

`ERC4626` is the standard that defines token vaults, or vaults whose shares (i.e. `ERC20` tokens) correspond to "shares" (i.e. percentage) of the vault's underlying token, a different asset. An inflation attack on one of the token vaults is when an attacker frontruns and deposits assets to change the relative value of the shares to the underlying asset, then the original user would have their shares devalued because of the sudden influx in assets. (Sources: [one](https://eips.ethereum.org/EIPS/eip-4626), [two](https://www.rareskills.io/post/erc4626), [three](https://www.alchemy.com/overviews/erc-4626))

> 82. How many arguments can a solidity function have?

The EVM is a stack based machine that can only access the top 16 items, theoretically making it so that if you were to make a function with 17 or more parameters, you would get a stack too deep error. ([Source](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#storage-memory-and-the-stack))

> 83. How many storage slots does this use? uint64[] x = [1,2,3,4,5]? Does it differ from memory?

The array `x`, a fixed-size array with values `[1,2,3,4,5]`, uses two storage slots. While fixed-size arrays use storage slot that are in sequence, which is not the case with dynamic length arrays, `x` uses only two storage slots versus five because each element in the array is of type `uint64`. Since each storage slot in a Solidity smart contract can hold 32-bytes, one storage slot can hold the first four of the elements in `x`. The fifth one would then be in the next slot, for a total of two storage slots ([source](https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/))

> 84. Prior to the Shanghai upgrade, under what circumstances is returndatasize() more efficient than push zero?

Prior to the Shanghai upgrade, `RETURNDATASIZE` was used to push `0` to the stack over `PUSH` because `RETURNDATASIZE` costs 2 gas while `PUSH` operations cost 3 gas. This could be seen implementated in the Minimal Proxy Standard implementation, as seen [here](https://blog.openzeppelin.com/deep-dive-into-the-minimal-proxy-contract). However, in the Shanghai upgrade, there was the addition of a new `PUSH0` opcode that accomplishes the pushing of a `0` to the stack that costs 0 gas. (Sources: [one](https://blog.quicknode.com/the-ethereum-shanghai-upgrade-what-you-need-to-know/), [two](https://eips.ethereum.org/EIPS/eip-3855), [three](https://www.evm.codes/))

> 85. Why does the compiler insert the INVALID op code into Solidity contracts?

The Solidity compiler adds the `INVALID` op code to Solidity contracts' bytecode to prepend the contract metadata at the end to prevent its execution. This is since the `INVALID` functions very similar to the `REVERT` opcode, with the difference being that `INVALID` consumes all of the transaction gas in addition to halting its execution. (Sources: [one](https://www.rareskills.io/post/solidity-metadata), [two](https://www.rareskills.io/post/ethereum-contract-creation-code), [three](https://eips.ethereum.org/EIPS/eip-141), [four](https://www.evm.codes/#fe?fork=cancun))

> 86. What is the difference between how a custom error and a require with error string is encoded at the EVM level?

Solidity only stores and returns only the first four bytes of a custom error, while Solidity has to store at least 64 bytes due to the string messages in `require` statements. (Sources: [one](https://www.rareskills.io/post/gas-optimization#viewer-a0fm0))

> 87. What is the kink parameter in the Compound DeFi formula?

Compound Finance is a borrowing and lending platform where users can supply digital assets to earn interest, and borrow assets while being charged an interest rate. As such, Compound uses an interest rate model to determine an utilization rate of a given money market. However, Compound also uses a _kink_ in their equation, which is a point that if the utilization rate were to pass, the interest rate increases faster, to act as a deterrent to "slow" down borrowing when liquidity is lower. (Sources: [one](https://docs.compound.finance/interest-rates/#interest-rates), [two](https://ianm.com/posts/2020-12-20-understanding-compound-protocols-interest-rates), [three](https://medium.com/the-quant-journey/defi-credit-lending-compound-cd1a26c561ee))

> 88. How can the name of a function affect its gas cost, if at all?

There are two ways a function's name in Solidity can affect its gas cost, and they are both related to if the function's selector has leading zeroes.

The first, because the EVM has to search for the functions that is being called when it receives the calldata, it will search through them based on their hexadecimal value. Thus if it is a function that is heavily used, it will be "found" first.

Second, it is more expensive to handle bytes that are zeroes than non-zero bytes. The zero bytes cost 4 gas, and non-zero bytes cost 16 gas.

As an added bonus, it will also make the contract code smaller and save costs on deployment. (Sources: [one](https://www.rareskills.io/post/gas-optimization#viewer-248d5), [two](https://github.com/jeffreyscholz/solidity-zero-finder-rust))

> 89. What is a common vulnerability with ecrecover?

The common vulnerability with `ercrecover` is that a valid signature can be turned into a different valid signature without requiring knowledge of the corresponding private key. This stems from the fact that the ECDSA that is used in Ethereum is a symmetric Elliptic Curve.

First, it's important to note that to authenticate validity of a signature, you need the `r`, `s`, and `v` values, as well as the signed message hash. And because the Elliptic Curve used is symmetric along its X-axis, the `s` value could be slightly altered to create another valid signature, which could lead to a signature replay attack. Worth noting that `EIP-2` was introduced as a response to this, which would make `s` only valid if it uses the lower levels. However, the precompile for `ecrecover` actually was not changed, so this vulnerability persists.

(Sources: [one](https://medium.com/immunefi/intro-to-cryptography-and-signatures-in-ethereum-2025b6a4a33d), [two](https://swcregistry.io/docs/SWC-117/), [three](https://docs.soliditylang.org/en/v0.8.23/units-and-global-variables.html#mathematical-and-cryptographic-functions))

> 90. What is the difference between an optimistic rollup and a zk-rollup?

Optimistic rollups begin with an "optimistic" assumption, and that is that transactions are valid until proven otherwise. On the other hand, zkRollups transactions are valid to be true by default due to zero-knowledge proofs. 

At a lower level, optimistic rollups have transactions executed off-chain and are batched together and submitted on the base layer one chain by a sequencer. As a security mechanism, validators are monitoring the transaction activity for any that are deemed invalid. To do so, they submit a fraud proof, and it has to be submitted within the challenge period.

zk rollups similarly execute transactions off-chain and submit them to the layer one chain via a summary of the state changes of the rollup with a zero knowledge proof to prove their validity. (Sources: [one](https://coinmarketcap.com/academy/article/optimistic-rollups-vs-zk-rollups-the-ultimate-comparison), [two](https://academy.binance.com/en/articles/optimistic-vs-zero-knowledge-rollups-what-s-the-difference), [three](https://chain.link/education-hub/zero-knowledge-rollup))

> 91. How does EIP1967 pick the storage slots, how many are there, and what do they represent?

`EIP-1967` details three storage slots that on proxy contract that hold special resposibilities. The three slots are those of the implementation, beacon, and admin address. The storage slots are generated as the hash of a string, detailing which slot it is, type casted to the `bytes32` type. The actual implementations for the three are as follows:
* `bytes32(uint256(keccak256('eip1967.proxy.implementation')) - 1)` 
* `bytes32(uint256(keccak256('eip1967.proxy.beacon')) - 1)`
* `bytes32(uint256(keccak256('eip1967.proxy.admin')) - 1)`

Overall, their purpose is to determine a consistent storage slot to search for each of the necessary "roles" (e.g. implementation) by others. For example, block explorers could look in the expected slot for a proxy's implementation in storage slot corresponding to `bytes32(uint256(keccak256('eip1967.proxy.implementation')) - 1)`, i.e. `0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc`, in the proxy itself. There, it will find the address of the implementation contract.

Most importantly, the main requirement for the storage slots chosen is that they must never be picked by the compiler to store any contract state variable. Otherwise, a logic contract could inadvertently overwrite this information on the proxy when writing to a variable of its own.([Source](https://eips.ethereum.org/EIPS/eip-1967#:~:text=This%20EIP%20proposes%20a%20set,to%20optionally%20act%20upon%20it.))

> 92. How much is one Szabo of ether?

1 Szabo of Ether is 10^-6 ([Source](https://etherscan.io/unitconverter))

> 93. What can delegatecall be used for besides use in a proxy?

Besides proxies, `DELEGATECALL` is used to make Library types in Solidity function.  (Sources: [one](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#delegatecall-and-libraries))

> 94. Under what circumstances would a smart contract that works on Ethereum not work on Polygon or Optimism? (Assume no dependencies on external contracts)

A smart contract that works on Ethereum may not work on Polygon or Optimism for things such as certain EVM opcodes not being implemented on the Polygon or on Optimism, and the precompile contracts behaving differently than Ethereum. (Sources: [one](https://eips.ethereum.org/EIPS/eip-3855), [two](https://www.rareskills.io/post/solidity-precompiles))

> 95. How can a smart contract change its bytecode without changing its address?

A smart contract can change its bytecode if it is what is a called a _metamorphic_ smart contract. This is done by the usage of the `CREATE`, `CREATE2`, and `SELFDESTRUCT` opcodes.

To carry this out, you would do the following:
* Create a _factory_ contract using `CREATE2` and also the ability to be destroyed via the `SELFDESTRUCT` opcode. This contract will also have the functionality to create **new** contracts with the `CREATE` opcode, and thus will be in charge of creating the actual malicious smart contract.
*  However, first you would need to create a benign smart contract with the factory by passing it non-malicious bytecode.
* Then both the factory and non-malicious factory-child contracts would be destroyed via `SELFDESTRUCT`.
* Afterwards, create the same factory using `CREATE2`, after making sure the same parameters are used, it would be deployed at the same address as before.
* Using the newly created factory, you would use it to create the non-malicious smart contract. And since `SELFDESTRUCT` and reset the factory's nonce, its address is the same, when the malicious contract is created with`CREATE`, it will have the same address as the initial non-malicious contract.

The above could also be altered to have an implmentation contract that the factory-child contract uses for its logic that is then destroyed and redeployed with the factory.

(Sources: [one](https://0age.medium.com/the-promise-and-the-peril-of-metamorphic-contracts-9eb8b8413c5e), [two](https://www.coinbase.com/blog/exploiting-governance-with-metamorphic-proposals), [three](https://a16zcrypto.com/posts/article/metamorphic-smart-contract-detector-tool/))

> 96. What is the danger of putting msg.value inside of a loop?

The dange of putting `msg.value` inside of a loop is that it will be reused. For example, if you have a `payable` multicall implementation that uses the `msg.value` of the transaction in each call in the loop, the value of `msg.value` will be reused for each call. (Sources: [one](https://samczsun.com/two-rights-might-make-a-wrong/), [two](https://blog.trailofbits.com/2021/12/16/detecting-miso-and-opyns-msg-value-reuse-vulnerability-with-slither/))

> 97. Describe the calldata of a function that takes a dynamic length array of uint128 when uint128[1,2,3,4] is passed as an argument

The call data for a function call to a function that takes `uint128[]` as an argument and passing it `[1,2,3,4]` as the argument would be as follows:
```
MethodID: 0xffffffff // Place holder function signature's first four bytes
[0]:  0000000000000000000000000000000000000000000000000000000000000020
[1]:  0000000000000000000000000000000000000000000000000000000000000004
[2]:  0000000000000000000000000000000000000000000000000000000000000001
[3]:  0000000000000000000000000000000000000000000000000000000000000002
[4]:  0000000000000000000000000000000000000000000000000000000000000003
[5]:  0000000000000000000000000000000000000000000000000000000000000004
```

The `0x00...0020` (`32` in hex) indicates the offset that the data starts. This mean it begins _after_ 32 bytes of calldata, _NOT_ including the calldata, which brings us to word `[1]`.

At word `[1]` you will find the amount of elements in the array, which in our case is `4`. In the subsequent words `[2]-[5]`, you will find each elements in the array passed as the argument `1`,`2`,`3`, and `4`. 

(Sources: [one](https://docs.soliditylang.org/en/v0.8.26/abi-spec.html#use-of-dynamic-types) | Example txn on Polygon Amoy [here](https://amoy.polygonscan.com/tx/0xdc3778a7f5fcade041f7409efe224ad530f7cf699c6c79a0cc5122fec3d878be))

> 98. Why is strict inequality comparisons more gas efficient than ≤ or ≥? What extra opcode(s) are added?

Strict inequality comparisons are more gas efficient than `≤` and `≥` because the former has the addition of supplementary `ISZERO` checks. (Sources: [one](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2), [two](https://www.rareskills.io/post/gas-optimization#viewer-7b77t))

> 99. If a proxy calls an implementation, and the implementation self-destructs in the function that gets called, what happens?

If an implementation contract has logic inside itself that uses `SELFDESTRUCT`, then the calling contract is destroyed. That is why a logic contract should not have the ability to `DELEGATECALL` into another contract, as that external contract could use `SELFDESTRUCT`, which would destroy the calling logic contract. If that logic contract then served as an implementation for a proxy, the proxy would have be trying to make calls to an address that no longer has any code. ([Source](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#potentially-unsafe-operations))

> 100. What is the relationship between variable scope and stack depth?

Similar to question 82 about how many arguments can be passed to a function, you are limited to having 16 local variables, like you are to 16 function arguments. That is because after that point, you will encounter "stack too deep" errors, as you would be exceeding the limit to the EVM stack depth. (Sources: [one](https://medium.com/@simon.palmer_42769/solidity-gotchas-part-4-stack-too-deep-929a0b488730), [two](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#storage-memory-and-the-stack))

> 101. What is an access list transaction?

An Ethereum access list transaction enables saving gas on cross-contract calls by declaring in advance which contract and storage slots will be accessed. Up to 100 gas can be saved per accessed storage slot.

This is implemented client side, and it allows the cold acess cost is paid upfront, rather than with the execution of an `SLOAD`. It informs the node clients ahead of time which storage keys will be accessed ahead of time, lowering the computational overhead.
(Sources: [one](https://eips.ethereum.org/EIPS/eip-2930), [two](https://www.rareskills.io/post/eip-2930-optional-access-list-ethereum))

> 102. How can you halt an execution with the mload opcode?

The `MLOAD` opcode can error, or halt execution, for two reasons. That would be for two reasons: running out of gas or not enough values in the stack.

The first, running out of gas, would be the reason behind something like a gas griefing attack. A type of gas griefing attack would be if a contract made an external call to another contract, and the called contract returned a payload too big that when the calling contract tries to load it to memory, the cost of memory expansion would be too much and would use up all of the gas.

The second would be there not being enough elements on the stack. If there is not an element on the stack for an `MLOAD` to pop off, then the execution would error out with a "stack underflow" error. (Sources: [one](https://www.evm.codes/#51?fork=cancun), [two](https://www.evm.codes/about#memoryexpansion), [three](https://blog.tenderly.co/how-to-debug-common-smart-contract-errors/))

> 103. What is a beacon in the context of proxies?

The Beacon proxy pattern allows multiple proxy contracts to share one logic implementation by referencing the beacon contract. The beacon contract provides the logic implementation contract address to calling proxies and only the beacon contract needs to be updated when upgrading with a new logic implementation address. (Sources: [one](https://eips.ethereum.org/EIPS/eip-1967#:~:text=This%20EIP%20proposes%20a%20set,to%20optionally%20act%20upon%20it.), [two](https://www.certik.com/resources/blog/FnfYrOCsy3MG9s9gixfbJ-upgradeable-proxy-contract-security-best-practices))

> 104. Why is it necessary to take a snapshot of balances before conducting a governance vote?

It is necessary to take a snapshot of token balances before a governance vote because users could vote, transfer their tokens to another account, and vote again, essentially re-using their tokens to cast multiple votes. (Sources: [one](https://www.rareskills.io/post/erc20-snapshot))

> 105. How can a transaction be executed without a user paying for gas?

The way a transaction can be executed without the end user paying for gas is by way of meta transactions. How they work is that a user signs a transaction then submits to a relayer/forwarder. This relayer then validates the transaction to be sent and submits it to the target contract. The target contract would also have to have the functionality to properly process meta transactions. (Sources: [one](https://www.alchemy.com/overviews/meta-transactions), [two](https://moralis.io/what-are-meta-transactions-exploring-erc-2771/))

> 106. In solidity, without assembly, how do you get the function selector of the calldata?

To get the function selector of the calldata, which would be only the first four bytes in the calldata, you could use the `msg.sig` transaction property. External and public functions can have their function selectors retrieved with the `.selector` member of the `function` type. (Sources: [one](https://docs.soliditylang.org/en/v0.8.26/units-and-global-variables.html#block-and-transaction-properties), [two](https://docs.soliditylang.org/en/latest/types.html#function-types), [three](https://www.rareskills.io/post/function-selector))

> 107. How is an Ethereum address derived?

There are two types of addresses in Ethereum: externally owned accounts (EOAs) and contract accounts. The creation of addresses for contracts is details in the response to question ninety-five. EOAs on the other hand, are generated from the right-most 160 bits of a Keccak hash of an ECDSA public key. ([Source](https://info.etherscan.com/what-is-an-ethereum-address/))

> 108. What is the metaproxy standard?

The MetaProxy standard, ERC-3448, can be thought of as an extension of the minimal proxy standard (i.e. "clone"), ERC-1167. The MetaProxy standard allows creating smart contract clones with an added unique immutable metadata for each of the clones. This unique metadata will be encoded and appended to the end of the clone's bytecode. (Sources: [one](https://eips.ethereum.org/EIPS/eip-3448), [two](https://www.rareskills.io/post/erc-3448-metaproxy-clone#:~:text=The%20MetaProxy%20standard%20is%20also,can%20have%20an%20arbitrary%20length.))

> 109. If a try catch makes a call to a contract that does not revert, but a revert happens inside the try block, what happens?

If a try/catch makes a call to an external contract that doesn't revert, but a revert happens inside of the `try` block, the transaction will still revert, because the execution continues to the `try` block if the external call was successful. This will cause an exception in the current contract and will cause the transaction to revert. (Sources: [one](https://docs.soliditylang.org/en/v0.8.26/control-structures.html#try-catch) | Example txn on Polygon Amoy [here](https://amoy.polygonscan.com/tx/0x5354809a4edc5932e8be1140a66705a8522a1e56428db07a9ba7ff7cf22dbf91))

> 110. If a user calls a proxy makes a delegatecall to A, and A makes a regular call to B, from A's perspective, who is msg.sender? from B's perspective, who is msg.sender? From the proxy's perspective, who is msg.sender?

From the proxy's perspective, `msg.sender` is the user.

From `A`'s perspective, `msg.sender` is the user.

From `B`'s perspective, `msg.sender` is the proxy.

(Sources: [one](https://ethereum.stackexchange.com/questions/123704/chain-of-delegatecall-and-call) | Example [txn](https://dashboard.tenderly.co/tx/polygon-amoy/0x4fcd3d7ff791246e0c2f88741bd9f466d5afcd5367f5c39e8db500b5f49beab6?trace=0.1.1.0))

> 111. Under what circumstances do vanity addresses (leading zero addresses) save gas?

When addresses are passed as function parameters, it saves more gas if they have leading zeros as the call data will have more zeros. Additionally, if the address needs to be stored, if you were to have two addresses with at least 4 bytes of leading zeros, they could both be stored into the same storaga slot, as each would only require 16 bytes.(Sources: [one](https://www.rareskills.io/post/gas-optimization#viewer-f970n), [two](https://medium.com/coinmonks/on-efficient-ethereum-addresses-3fef0596e263))

> 112. Why do a significant number of contract bytecodes begin with 6080604052? What does that bytecode sequence do?

These 5 bytes, `6080604052` represent the initialization of the “free memory pointer”. The free memory pointer is simply a pointer to the location where free memory starts. It ensures smart contracts keep track of which memory locations have been written to and which haven’t. This protects against a contract overwriting some memory that has been allocated to another variable.([Source](https://noxx.substack.com/i/49480285/free-memory-pointer))

> 113. How does Uniswap V3 determine the boundaries of liquidity intervals?

Uniswap V3 introduced the concept of "concentrated liquidity" to their protocol. With this, each token pair's pool can have its liquidity allocated within custom price ranges. Prior in V2, all liquidity was distributed all along the price curve between zero and infinity. 

How UniswapV3 determines the boundaries of these liquidity intervals is the usage of "ticks". Ticks are the boundaries between discrete areas in price space. Ticks are spaced such that an increase or decrease of 1 tick represents a 0.01% increase or decrease in price at any point in price space. (Sources: [one](https://docs.uniswap.org/concepts/protocol/concentrated-liquidity))

> 114. What is the risk-free rate?

The risk-free rate represents the interest an investor would expect from an absolutely risk-free investment over a specified period of time. (Sources: [one](https://www.investopedia.com/terms/r/risk-freerate.asp))

> 115. When a contract calls another call via call, delegatecall, or staticcall, how is information passed between them?

When a contract calls another via the `CALL`, `DELEGATECALL`, and `STATICCALL` opcodes, the information is passed to the callee as ABI encoded, which would include the called function's selector along with the arguments. Additionally, because these are low-level ways of calling contracts, you would need check the returned `bool` to know the status of the execution, and would need to decode the returned payload.  (Sources: [one](https://www.rareskills.io/post/low-level-call-solidity), [two](https://docs.soliditylang.org/en/v0.8.26/units-and-global-variables.html#abi-encoding-and-decoding-functions), [three](https://docs.soliditylang.org/en/v0.8.26/units-and-global-variables.html#members-of-address-types))


## Advanced


> 116. What addresses do the ethereum precompiles live at?

The precompiled contracts are at addresses 0x01 through 0x09. ([Source](https://www.evm.codes/precompiled?fork=shanghai))
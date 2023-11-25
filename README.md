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
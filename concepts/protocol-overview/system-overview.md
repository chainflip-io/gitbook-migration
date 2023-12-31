# System Overview

Sometimes called a “Cross-Chain Liquidity Network,” the core of the Chainflip idea is to use MPC (Multi-Party-Computation), and in particular TSS (Threshold Signature Schemes) to create aggregate keys held by a permissionless network of 150 **Validators**.&#x20;

These Validators control simple smart contracts/wallets called **Vaults**, on multiple blockchains simultaneously, giving rise to a fully decentralised 'settlement layer.' This is paired with an 'accounting layer', which uses the Substrate-based Application Specific Blockchain that we call the Chainflip **State Chain** to track balances, process events, and execute instructions. These systems give rise to a **fully decentralised general cross-chain swapping protocol**.

In practice, the codebase has no such thing as an 'accounting' or 'settlement' layer, but explaining the system into two layers is a helpful way to conceptualise its structure.

The assets used for trading are held in the Vaults on chains such as Ethereum, Bitcoin, and so on, with all of the trading and account-keeping occurring on the State Chain. This means that swap execution and [AMM design can be heavily customised ](../swaps-amm/just-in-time-amm-protocol.md)for the specific use case of cross-chain value transfer.

The protocol's accounting is fully contained in the [**State Chain**](../validator-network/the-state-chain.md), and includes the [**Chainflip Just In Time (JIT) AMM**](../swaps-amm/just-in-time-amm-protocol.md)**.** It is based on the Uniswap v3 AMM design, but unlike Uniswap v3, the JIT AMM is not represented as a series of smart contracts on a single blockchain environment. Rather, the JIT AMM is **virtual**. This means that funds are not held directly in on-chain pools using wrapped assets, but instead traded virtually on the State Chain as account balances, and settled using the actual assets held natively in Vaults by the protocol.\
\
This works in a similar fashion to how centralised cryptocurrency exchanges store all of the deposits in a unified wallet system, with the balances of individual users tracked in separate accounting and trading systems.

<figure><img src="../.gitbook/assets/systemdiagram.jpg" alt=""><figcaption><p>Overview of the flow of funds</p></figcaption></figure>

Trading and tracking assets virtually on the State Chain dramatically simplifies the work needed to support individual chains, as rather than needing to write swapping logic in a range of smart contract and scripting languages on external blockchains, it is entirely contained within the Chainflip State Chain environment, meaning all of the abstractions needed to support a given chain can be managed entirely within the 'settlement' layer, and are much simpler in comparison.

## The Validators of the Chainflip Network&#x20;

A Validator, in the context of Chainflip, is a server operating a **Chainflip State Chain node**, as well as additional ‘sidecar’ functionality through the **Chainflip Engine (CFE)**, an off-chain processing module. For the purposes of introduction, at a high level:

* Up to 150 Validators can participate in the protocol’s **Authority Set** at any one time, and collectively operate almost all of the protocol's functions concurrently, and jointly control _all_ funds in the system.
* All Validator slots are entirely permissionless, where any Validator operator with sufficient $FLIP to outbid others in [Auctions](../validator-network/validator-auctions-bonds-and-rewards.md) becomes a member of the Authority Set after winning.&#x20;
* Each individual Validator has its own set of private keys which it uses to participate in the consensus of the State Chain, as well as to generate secrets for the purposes of TSS key generation and signing.
* Every Validator simultaneously watches all supported external chains for **Witnessing**, which is functionally equivalent to the network being its own decentralised oracle for incoming transfers.
* Using the TSS protocol in chorus with the other Validators and the external blockchain connections, Validators also **Broadcast** transactions to send funds out of the protocol. Again, the network is its own decentralised relayer.

Validators in the **Authority Set** perform the following functions:

* Accounting Functions
  * Maintain and update the **State Chain** through Aura proof of stake consensus, producing blocks and executing extrinsics and events such as swaps, liquidity updates, deposit channels, supply updates, and much more.&#x20;
* Settlement Functions
  * Participate in TSS Keygen ceremonies to create or rotate control over Vaults.
  * Participate in TSS Signing ceremonies to construct valid transactions for external blockchains, and broadcasting those signed transactions to the mempools of those external networks (**Broadcasting/Egress)**.
  * Monitoring and Parsing incoming transactions to vaults on external chains to be Witnessed on the State Chain. (**Witnessing/Ingress**)

Validators are covered in more detail in the [Validator Network section](broken-reference). With this high-level overview of the network laid out, let’s use it to understand more components and how they come together to solve the cross-chain swapping problem.

### Constructing Vaults&#x20;

Fundamental to any native asset swap flow is for there to be a pool of liquid native assets on each supported chain. With no source of on-chain liquidity, there would be nothing to swap. At the ground floor of any credible native cross-chain swap protocol, a general solution to flexible liquidity storage must be engineered. In this section, we’ll pull apart what a **Vault** is and where it fits into the wider protocol.

> “A fundamental limitation of other interoperability protocol designs is that they lack the ability to secure native funds on host blockchains. So called ‘messaging’ or ‘interoperability’ protocols offer insufficient functionality to carry out large scale cross-chain trading. These protocols only enable 1 to 1 (pairwise) swaps, which limit their utility to users. This type of protocol design cannot fulfil the functionality ultimately necessary to render a centralised exchange offering obsolete, and in turn might explain why they have achieved limited adoption.”

![](<../.gitbook/assets/he Settlement and Accounting Layers of Chainflip-2.1.png>)

A **Vault** is a method of storing funds on a given blockchain that is controlled by the Chainflip protocol. This could come in a few forms, with different blockchains offering different features and limitations, but broadly there are two categories of vault which are used to store funds to be used in the AMM:

* **Vault Contract:** A [smart contract (EVM or non-EVM)](../components/evm-ethereum-vault-design.md) that holds a pool of assets on a given chain. The Vault smart contract can send funds with the approval of an aggregate key held in the KeyManager contract. The aggregate key is controlled by the Authority Set in a 100 of 150 threshold signature scheme (FROST) handled off-chain. When the Authority Set is changing, the old Authority Set approves a transaction that changes the aggregate key from the outgoing Authority Set to the new aggregate key, controlled by the new Authority Set in the KeyManager contract.
* **Native Wallet Vaults:** A native wallet or set of wallets controlled by the Authority Set in a 100 of 150 threshold signature scheme and is handled off-chain. As with the vault contract above, the native wallet vault still relies on the FROST threshold signature scheme to function. The details of the native wallets vary from blockchain to blockchain, but rely on native multisig schemes offered as features on each blockchain. Funds are held in each Native Wallet Vault until a new Authority Set is determined, after which a **Vault Rotation** occurs. This could involve the old Authority Set delegating access to the Vault Account on-chain, or the new Authority Set will create its own Native Wallet Vault, and the outgoing Authority Set will hand over their keyshares to the new set and sweep funds from its own Wallet Vault into the new one. For an example of this in practice, check out the [Bitcoin Vault](../components/bitcoin-vault-design.md) page.

Although not used for liquidity pools, there is also a third protocol-controlled vault-like contract that fills a special function called the:

* **State Chain Gateway Contract:** The [**State Chain Gateway**](../validator-network/state-chain-flip-gateway.md) is a smart contract (initially just on the Ethereum network) which holds $FLIP funds as collateral for accounts on the State Chain. Like the Vault Contract & Native Wallet Vaults, signature verification is delegated to the KeyManager contract, which verifies an aggregate signature produced by the current Authority Set. Unlike the Vault Contract, users directly interact with this contract, but can only redeem $FLIP from this contract with a valid signature from the aggregate key - or in other words, with the off-chain approval through a transaction signed by the Authority Set.

In all of these cases, a new Authority Set must perform all of the necessary key-generation ceremonies for all deployed vaults before authority over the State Chain consensus, Vaults, and the State Chain Gateway contract are handed over.

No matter the underlying implementation of each Vault, the end result is that there is an account on each supported blockchain under the control of the Chainflip protocol. This forms a functional, decentralised, and generalised Settlement Layer on which liquidity pools and staking functionality can be virtually constructed using assets held in these vaults.\
\
In the next section, we'll explore the Validator Network and the State Chain that coordinates them in greater detail.

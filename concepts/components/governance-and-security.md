# Governance & Security

## Governance Processes

The Chainflip protocol will not be set in stone. A governance process must exist in order to upgrade the network to support new blockchains, new pools, update fees, and adjust rewards as needed. Further, governance processes can be implemented which could significantly reduce the potential impact of unexpected events such as supermajority attacks, ransom attacks, and software exploits.

However, in order to preserve the decentralised nature of the protocol, the following principles must be observed:

1. Governance processes in a decentralised protocol should not be able to be unilaterally executed by any one party.
2. An honest Validator network should be able to expel any nominated governance body.
3. Most importantly, effective control over user funds should never be given to any single party. Effective control means the ability to unilaterally control the flow of funds, or to permanently block access to funds.

In this section, we present a range of governance features of the Chainflip protocol designed to upgrade and protect it.

### The Governance Keys

The Governance Keys are arbitrary keysets that are nominated implicitly by the Validator network. The keys themselves can be any multisignature scheme that is compatible with the governance pallet that Chainflip Validators run. What this means is that the Governance Keys could be managed by any Threshold Signature Scheme, standard n of m scheme, or even a single key (though this would likely never be voted for). Who is behind said keys is a social matter - DAOs, councils, companies or even single individuals could be elected as a governance keyholder with enough backing, though we will refer to them generally as “Councils.” A supermajority of Validators can at any time propose to elect a new Council.

There are two Governance Keys in the Chainflip protocol, each held by different Councils and used for different purposes.

1. **The Governance Key**: This key holds many responsibilities, and is intended to be held by the primary developers of the protocol, or a body that represents them. Outside of managing the upgrades process, the 'Governance Council' can activate essential governance processes which aim to protect the protocol from attacks, and software exploits. However, these security features of the Chainflip protocol often require an additional signature from the Community Council to enable the full governance capabilities built into the protocol.
2. **The Community Key**: This key acts as a check-and-balance for the Governance key. Intended to be held by a body of community members separate to the primary developers of the protocol, this key has no special abilities by itself, but it is required to allow the Governance Key to use the more powerful security and governance features of the protocol. If used correctly, this key prevents the Governance keyholders from being a threat to the protocol, or exerting unilateral and effective control over the protocol through high-stakes governance actions.&#x20;

The reason the role of check-and-balance isn’t simply left to the Validators by default is because there may be scenarios where they aren’t the best arbiters of community interests. This is especially true in the unlikely cases where some or all Validator keys have been compromised by hostile attackers. Further to this, Validators make up the heart of the protocol, but may not share the same views as users, liquidity providers, and developers that are also essential to any Web3 protocol’s long term success. By forcing Validators to nominate a distinct and smaller governing body, the network can enact governance votes faster and with fewer conflicts of interest.

The keys are automatically deployed to Smart Contract Vaults and the State Chain Gateway contract by the Validator network, though a cooldown period is enforced to mitigate against attacks involving software exploits.

### Voting in New Keys

Once the protocol is up and running, the Governance Council and the Community Council can be rotated only through a token-weighted vote on the state chain. Anyone with a token balance on the State chain can vote in proposals, even those currently staked into Validators. The minimum threshold for a successful proposal is a 2/3rd+1 super majority.

Anyone can create a new governance process to vote on the replacement of either one of the keys on the State Chain at any time. Voters must opt in to any key rotation proposal in order for a vote to be counted. The proposals last for 7 days, and the token balances are only counted at the end of the proposal timeframe. Anyone that is actively unstaking or undergoing claims at the end of the vote will not have their tokens counted towards the result.

In order to mitigate against a scenario where a hostile supermajority has overridden this process in an attempt to use the security features against the protocol and its users, a cooldown period is required. By enforcing State Chain rules which prevent the rotation of both governance keys for a 14 day period, a hostile supermajority will have to wait a week before the new key is installed and usable.

Liquidity providers should therefore receive as much as 2 weeks warning that the protocol is being taken over by a new party that may or may not be connected to the existing users and developers of the protocol. From there, they can decide if they wish to keep their assets in the system, or withdraw before any actions can be conducted by the new keyholders. Similarly, validators may wish to unstake during this cooldown window if they decide they do not support the holders of the new governance keys.

Thus, any party can become a Governance keyholder, but it requires not only support from a supermajority of Validators and other token holders but also backing from the liquidity providers in place.

## Runtime Upgrades & Hard Forks

The Runtime Upgrade process, which is enabled by the Substrate blockchain framework, changes the default nature of protocol upgrades from the traditional forking system, one which relies on manual binary upgrades, into one whereby the Governance Council has the ability to upload changes to the blockchain code directly to each individual node, and at a specified blockheight, have the responding nodes switch over to the new protocol rules seamlessly. This is a great feature to enable fast and lower-friction development, but doesn’t work perfectly in Chainflip.

Due to the Chainflip Engine, a sidecar binary that runs outside of the State Chain runtime, Chainflip Validators won’t always be able to be seamlessly upgraded in this way. If any changes in a runtime upgrade also require a change in the Chainflip Engine, Validators that have not updated this secondary binary may be suspended by the other Validators when they can’t meet the newly required functionality specified in the runtime upgrade.

Traditional forks are also still possible using the Grandpa finality module that ships with Substrate, although we suspect that the governance processes designed for the protocol will be a more appropriate method for both upgrades and for making major project direction changes amongst competing groups of participants by voting on new governance keys.

## Security Considerations & Features

Decentralisation presents a challenging array of security considerations that centralised exchanges simply don’t have to deal with. Previous attempts at secure cross-chain systems have proven to be challenging in the wider ecosystem, with losses from security incidents in the cross-chain sector easily reaching into the hundreds of millions of dollars. We have thought long and hard about Chainflip’s protocol security, and take it very seriously. Here, we’ll explore some of the security properties of the Chainflip protocol, the range of security risks we have identified, the assumptions that we’ve made, and the features designed to mitigate those risks.

### Potential Protocol Risks

No security profile can be built without a firm understanding of the risks to which the protocol is exposed. The surface area for faults and exploits with bad outcomes for protocol participants is not infinite, but it is larger than most blockchain projects. To begin, let’s enumerate where things could go wrong:

1. Liquidity could be lost or stolen through the AMM/State Chain logic - LPs and swappers could experience potentially serious or even complete losses if the AMM logic or other related accounting systems within the blockchain fail or are exploited. By undermining the intended logic of the Accounting Layer, Validators could distribute funds incorrectly, leading to incorrect payouts. Attackers, if such exploits are discovered, could use them to drain funds into their own wallets. This type of security issue has happened in THORChain, for example.&#x20;
2. Liquidity could be lost or stolen through the vault management system - LPs and swappers who have deposited funds and are waiting to be paid out could experience potentially serious or even complete losses if the Chainflip engine is compromised or exploited in this way. Close attention needs to be paid to TSS ceremonies, broadcasting logic, and key rotation processes in the vault management system.&#x20;
3. Validator private keys could be compromised, seriously undermining the security of the system as a whole if enough keys are compromised. Superminority and supermajority attacks could occur if this happens at a large enough scale. Individual Validators could also have all of their $FLIP stolen if their Validator key is compromised.
4. Validator private keys could be simply lost, which if it occurs could result in the liquidity held by Validators being unable to be moved, potentially forever if enough keys can not be recovered.
5. The $FLIP token market could collapse if the code which handles emission, rewards, and claims is compromised or exploited.
6. External chains could be attacked or experience major reorgs which would likely break the Chainflip Accounting Layer if incoming transactions that have been witnessed are later rolled back on the external chain.

## Protocol Level Security Features

Many of the above risk areas can largely be addressed by enforcing good engineering processes, a comprehensive integration and unit testing suite, comprehensive internal and external audits, and offering significant bounties to penetration testers. However, any complex software system that assumes that everything will work as intended, even if all of the correct precautions have been taken, has failed to develop a comprehensive security plan.

Redundancy is a key security principle and especially when applied in complex distributed systems with many moving parts like the Chainflip protocol. Our unique security features are designed to offer redundancy during many potential security incidents.&#x20;

### State Chain Safe Mode

As all of the above risks will impact the State Chain, and in fact most of the risks could arise from State Chain code, a system to pause all critical chain activity is essential in the case of a security incident.

The Safe Mode is a chain state where Validators temporarily agree to halt most core functions of the protocol. Blocks will continue to be produced, so that key governance extrinsics and events can be processed, but other functionality is heavily reduced. In Safe Mode:

* Witness extrinsics from the block at which the Safe Mode was activated are not accepted. Any ingress events that are not finalised will have to wait until after Safe Mode is deactivated to be rescanned and processed.
* Auctions and vault rotations are no longer automatically triggered.
* No Egress transactions are processed (No swap payouts, no LP withdrawals, and no new staking claims will be processed).
* Supply sync transactions are suspended.

Validators can choose to opt-in or opt-out of **Safe Mode delegation**. This is done during the registration process and can be changed by submitting an extrinsic to the chain at any time. Validators which opt-in to Safe Mode delegation defer their actions to that of the Governance Council, meaning if the Governance Council submits a “safe mode request” governance extrinsic to the chain, those validators automatically count as having voted to enter Safe Mode.

A threshold of only 50% of the Authority Set needs to vote in favor of Safe Mode to activate it. Turning it on gives Validators, developers, and the community time to analyse security incidents in a reduced risk state. During Safe Mode, runtime upgrades and other fixes can be deployed to the network, allowing for a measured recovery process.

Once the incident is resolved, the network can exit Safe Mode by a supermajority vote on a governance extrinsic that can be submitted by anyone. Once again, opted-in Validators delegate their vote to the governance key.

### State Chain Gateway Protection

The State Chain Gateway is an Ethereum smart contract that allows holders of $FLIP, an ERC20 token, to put up collateral on the Chainflip State Chain. This collateral is credited on the State Chain as an account balance which can be used to run Validators, Brokers, or perform active liquidity management activities. The State Chain Gateway contract also processes redemptions to send unlocked $ERC20 tokens back to account holders after receiving a valid redemption certificate (a TSS 101 of 150 signature from the current Authority Set). The State Chain Gateway can call the $FLIP Token contract to mint more tokens if required as well.

There are quite a few different ways this could be exploited. Namely, risks 1, 2, 3 and 5 are all relevant and could all cause losses if the State Chain Gateway contract is involved. To provide a layer of protection against risks 1, 2, 3, and 5, the State Chain Gateway comes with a feature which adds a 2 day delay before a user can actually claim tokens (executing a claim) from the contract after submitting a withdrawal certificate (registering a claim) to the smart contract. The delay itself changes little, but in combination with its second feature, it can play a major role in providing redundancy against attacks and losses related to bridging $FLIP.&#x20;

The second feature allows the Governance key to suspend the State Chain Gateway from executing redemptions, or register new ones. If an issue where fraudulent or incorrect registered credemptions are detected within the delay window, negative impacts from $FLIP bridging-related incidents can be negated.

That being said, freezing claims indefinitely is not a solution. Any registered false redemptions would be able to be executed when the State Chain Gateway is unfrozen again if claims did not expire after some period of time. As the State Chain Gateway enforces an expiry time, which is set at the time of registering the redemption, the executions can not be processed by the State Chain Gateway contract after 3 times the length of the claim delay. In other words, on mainnet all unexecuted redemptions expire after 144 hours.&#x20;

This means that in a security incident where the State Chain Gateway is frozen, a graceful recovery is possible if underlying issues on the State Chain can be resolved and the State Chain Gateway is unfrozen after all registered claims have expired, which should be within 144 hours.

During this time, the State Chain and Vaults could continue operating, unless they too have been affected by the incident, in which case Safe Mode is likely to also be active.

NOTE: The current iteration of the State Chain Gateway contract features a governance withdrawal function in tandem with the community key. It is unclear if this is necessary or desirable going forward, and its removal is slated for discussion.

### Contract Vault Protection

Risks 1, 2, 3, 4, and 6 apply to the Vaults, and the losses that could be experienced due to these risks would impact liquidity providers, who could lose all of their funds, as well as swappers who are waiting for a payout.

Smart Contract Vaults also come with similar capabilities as the State Chain Gateway, however unlike the Gateway, there is no delay on payouts from the Vaults. This is necessary to ensure fast swapping times for users, but it does make the protections weaker than in the State Chain Gateway contract.

All smart contract based Vaults can be frozen by the Governance Council. Upon witnessing this function being called, the Validators will no longer be able to process egress transactions from that Contract Vault. It is assumed that Safe Mode may already be active at this time in most scenarios, so this could already be the case, but does add an additional layer of protection for the large pools of liquid funds held in these vaults.

While frozen, the Community Council plays a very significant role. If it is assessed that the contract or the network could be irreversibly compromised, or that the aggregate key has been lost or corrupted for good, a final option exists to ensure that funds remaining in the Contract Vaults can be recovered and returned to users and liquidity providers.&#x20;

Within the Contract Vaults, the Community Council can call a function that authorises the Governance Council to withdraw all funds in the vault. This check and balance ensures that the Governance key never has unilateral control of the funds and must first both freeze the vault contract and then achieve approval from the community key before this extreme recovery method can be used.

This protection feature is considered very important considering that it is expected that over 80% of all assets held on the AMM are likely to be held in Contract vaults, as opposed to Native Wallet vaults.&#x20;

### Security Ratio

Although assessed to be incredibly unlikely, even without this security feature, to prevent financially motivated attackers from benefiting from a collusion attempt the Security Ratio is enforced by the Validator network. As the chainstate is updated with ingress, egress, and trade information, it is possible to deterministically calculate the value of assets controlled by the validator network as well as the value of $FLIP staked in validators.&#x20;

Because only two-thirds of the validators are required to conduct a supermajority attack, the total value stored in the Vaults should not exceed the locked collateral of two-thirds of the validator network. This is the security ratio, where:

```
Security Ratio = LiquidityTVL : Collateral * ⅔
```

For a variety of reasons, it is assessed that exceeding this ratio presents no immediate or significant danger, unless the ratio climbs well beyond 1:1. For example, a ratio of 2:1 would imply that the value of the stored TVL would yield a theoretical maximum attack profit of 100% return if a perfect supermajority attack was conducted. However, it is not clear that a one off 100% yield would be enough to justify executing a supermajority attack, given the opportunity cost of future validator returns, collateral appreciation, the risk of failure, complete loss, and real world criminal prosecution. The practical risk should be continuously assessed and the security ratio reviewed if it becomes a limiting factor in scaling the protocol.

In any case, the Security Ratio of 1:1 will be enforced by the validator network to prevent buildups of collateral not being fully utilised.

When the security ratio approaches or exceeds 1:1, the Validators will be required to automatically purge liquidity held by liquidity providers. Given that refund addresses have already been provided, it is possible to initiate an automatic egress return transaction for any liquidity provider at any time.

When the conditions for purging have been met, Validators will deterministically select liquidity used the least over the last 30 days. Any liquidity not yet deployed in pools would be the first to be purged. Secondly, liquidity held in unconcentrated ranges would be purged next, and so on, until the Security Ratio is reduced to below 1:1.&#x20;

Deposits to liquidity positions will also be blocked when the ratio reaches 1:1. To prevent disruption to active liquidity strategies in the JIT AMM, only the least active liquidity providers and new liquidity providers will be blocked from making deposits until the ratio drops below 1:1 again. If the ratio exceeds 1.3:1, then all deposits will be blocked.

Given it is possible that large swaps by users could push the ratio above any of these thresholds temporarily, the security ratio is calculated using rolling averages at regular time intervals rather than instantaneous calculations. This also helps reduce the frequency of on-chain calculations required.

### Supermajority Attacks

A hypothetical existential threat exists in the Chainflip protocol (and for that matter, all decentralised protocols): the Supermajority Attack. Chainflip requires a signature from 101 of the 150 Validators to produce valid transactions to move funds on external chains. If a malicious supermajority were to exist, they could easily steal the funds in all vaults. Likewise, a supermajority would be required to alter State Chain history, confirm false witness transactions, or other potential actions which could result in a loss of protocol funds.&#x20;

The Chainflip protocol guarantees that as long as an honest superminority (a blocking vote of 50 nodes) exists in the Authority Set, and the protocol has not malfunctioned, all witnessing, State Chain execution, and egress transactions on other chains will be executed deterministically, with no scope for falsification.

If a supermajority of the Authority Set colludes or has their keys compromised, then it should be assumed that all funds could be stolen, and complete disruption, takeover or failure of the network is possible. Chainflip provides no security guarantees in a supermajority attack situation, and in fact nor does any other credibly decentralised blockchain network of any kind. Correctly designed economic security systems are intended to prevent supermajority attacks, but the possibility of one can never be totally ruled out, and so is treated as an accepted risk, though we strongly believe it will never occur.

The risk of collusion is mitigated mostly through the enforcement of the Security Ratio, though without guaranteeing complete protection. It will always be possible in decentralised networks that if enough irrational actors exist, collusion can occur. However, we assume that there will always be a rational superminority in the Authority Set. Extensive reasoning on this subject is possible, though we don’t think it's worth discussing here.

Another vector for a supermajority attack is a wide-scale validator key compromise event. However, using modern server architecture, it shouldn’t be possible to compromise over 100 individual servers. Validator operators are also incentivised to secure their keys, as exposing them could result in all of their own staked $FLIP being stolen. However, maintaining secure Validator nodes is essential, and software exploits (not just in Chainflip software, but also the operating system and other packages running on the machine) that allow for remote code execution or root access could potentially expose the keys of multiple Validators if they share the same exploitable architecture, which could potentially lead to a supermajority attack.

That being said, aside from setting sensible defaults for the software that is shipped and the support that is given, Validator server security isn’t really protocol design and therefore must be considered another accepted risk. If a common exploit that could expose private keys existed across 100 servers running Chainflip Validators, such as a zero-day exploit in a common Linux distribution, it’s likely that the exploit could be used to target much bigger and easier targets, and would likely result in mass security breaches across dozens of high-value software protocols globally. Aside from encouraging Validator operators to keep up to date with security patches, there’s not a lot that Chainflip developers can do about that, and so it’s an accepted risk that at least a superminority of Validators will remain both honest and secure.

### Superminority Ransom Attack

An attacker that controls a superminority (>1/3rd) of the Authority Set can block all actions in the protocol, effectively freezing all funds, which could be held for ransom. This way, the attacker can extract value from the users without ever having to acquire the full 2/3rds of validators required to pull off a complete theft of the pool.&#x20;

In practice, we consider this incredibly unlikely, especially considering that the Vault contract protections would allow the governance and community keys to extract the bulk of value out of the system (everything held on EVM chains, which includes all USD), limiting the upside potential of the attack. The downside is very likely the complete loss of FLIP collateral used to acquire the superminority, which if the Security Ratio is enforced, will be worth more than the remaining funds being held for ransom by the attacker, making the attack unprofitable.

#### Questions?

If you have any thoughts or questions on the contents of this page, please head to the Discord!

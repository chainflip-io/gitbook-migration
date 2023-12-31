---
description: How the Auction system came to be.
---

# Auction Theory (SSOD)

## **What the Validators Do and Why It’s Important**

At any given time, Chainflip is secured by up to 150 Validator nodes forming the Authority Set. These nodes collectively secure the state of the Chainflip internal chain (called the State Chain), as well as the capital in the system (Liquidity) provided by Liquidity Providers. In return for this, Validators earn a reward from emissions.

The Validator network makes up _the_ key infrastructure of Chainflip. Validators in the Authority Set regulate: block production by consensus; all ingress; all egress; all of the swapping logic; all of the witnessing logic; all of the staking; all of the emissions and rewards; and all of the network upgrades.

That’s _a lot_ of responsibility. Economic security is paramount in Chainflip, as the vaults depend on an honest superminority of Validators to remain secure. Larger Authority Sets make collusion progressively more challenging.

**Why Not Have One Million Nodes?**

On the _flipside_, more Validators means worse scalability. The threshold signature schemes employed scale progressively worse with every signatory that gets added. A 1000 node signing set compared to a 100 node signing set is much more than an order of magnitude slower when producing signatures and keygen ceremonies. With Taproot now running on Bitcoin, we don't need GG20, which greatly improves scaling possibilities, and therefore the potential node count and networks that can be supported. However, there are still limitations that must be considered.

Like all other decentralised projects, a tradeoff must be made between decentralisation and scalability. Chainflip’s Validator count at launch will be 150. This may increase over time with improvements to threshold schemes or sharding of keysets, but in the meantime, we’re presented with a challenging economics question to answer: how should these Validator slots be allocated in a permissionless system?

### **The Auction System: Allocating Validator Slots**

![](../.gitbook/assets/FS4utSYX0AgBY2k.jpg)

There’s a range of ways that protocols allocate positions of relative authority in consensus networks. [DPoS](https://en.bitcoinwiki.org/wiki/DPoS), [PoA](https://en.wikipedia.org/wiki/Proof\_of\_authority), [Eth2.0 style PoS](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/), and [unlimited node counts with fixed staking requirements](https://docs.oxen.io/about-the-oxen-blockchain/oxen-service-nodes) are just a handful of examples. With the exception of the few collusion-prone blockchains that have a tiny Validator set for _hypercharged mega-totally-safe-and-scalable blockchains of the future_, most permissionless networks don’t have a cap on the number of Validators allowed on the network. Chainflip does, which places some constraints on the allocation system, and means we have to think a _little outside the box_ to come up with a workable plan.

The goals of the allocation system are as follows:

* Maximise the collateral locked in Validator nodes
* Encourage a relatively even distribution of collateral across the Validator network
* Minimise active involvement in the auction process for adequately collateralised nodes (reduce the mental bandwidth required to run a node)
* Encourage a stable Validator set without excluding new entrants
* Minimise gas costs associated with participating in the process

To achieve this set of goals, we’ve designed a minimally-interactive system to allow market dynamics to handle much of the process of allocating slots. It’s been partly inspired by the winners of the[ 2020 Nobel Prize in Economics, Robert Wilson and Paul Milgrom](https://www.nobelprize.org/prizes/economic-sciences/2020/press-release/), who through their studies in auction theory designed a system for equitably selling off a set of partially-fungible slots in a finite set called SMR (Simultaneous Multi-Round) Auctions. The most prominent example of this was when the [FCC sold off broadcasting frequencies in the US to great effect](https://www.fcc.gov/auctions/auction-designs) using this system. Here’s a [sweet video from Economics Explained](https://www.youtube.com/watch?v=R\_wxk7Ihyok) about it and also some broader concepts on Auction Theory.

Chainflip’s auction process shares some concepts with SMR auctions but does have notable differences. Perhaps a better name for this style of auction could be a **Simultaneous Single-Round Open Dutch (SSOD) Auction**, as while participants are only participating in a single continuous round, they are incentivised to _openly place the maximum bid_ they can as there is _no downside risk_ to paying too much. Here are the rules for the auction process:

1. The auction starts halfway between the start and the expected end of the Epoch.
2. Any amount that is in a bidding party's account at the start of the auction will be automatically counted as a bid and remains locked for the duration of the auction (this includes existing Authorities and any rewards they earned before the auction started).
3. New bids that are placed either top up existing bids or enter a new candidate into the auction. Additional bids can be placed at any time during the auction, however once placed, they remain locked until the end of the auction.
4. The auction resolves at the end of the epoch, as follows:
   1. The bounds for the size of in the next authority set are determined according to the previous set size and a fixed maximum of 150 (or any other arbitrary Authority Set Size Cap). Where the previous set size was less than 100, the growth of the set size is limited to an additional 50%.
   2. The 150 highest bidders are designated as primary keygen candidates. Any remaining bidders who were authorities or backup nodes prior to the auction are retained as secondary keygen candidates.
   3. The token bond amount is defined as the minimum bid of all winning bids (Minimum Active Bid). The bond amount is the amount of tokens that are locked for each Authority and cannot be withdrawn until the associated keys expire. In most cases, expiry happens after the following auction, when control of the vault is passed to a new key, controlled by a new Authority Set.&#x20;
5. The new proposed primary canidates are then required to cooperate to generate new aggregate threshold keys. If, during this key generation process, there are any nodes that fail to participate, these nodes are added to an exclusion list, and we replenish the keygen candidates, up to the desired set size limit, from the pool of secondary candidates determined during auction. Inclusion of these candidates does not affect the bond, which was fixed during the auction.&#x20;
6. After the key generation ceremony is complete, the auction is deemed successful, and all failed bidders may withdraw their stakes. All Authorities may also withdraw any amount of tokens in excess of the now-locked bond from their stake.

This system is used because it avoids expensive on-chain last minute bidding wars with all participants trying to stake the minimum possible whilst winning slots. In this SSOD Auction system, each Validator should just bid the absolute maximum they can because at the end of the auction, they _always_ have the option of withdrawing whatever they didn’t need. It’s only the bottom set of Validators that may need to quickly top up their bids before the end of auctions in order to protect their existing slots. It should also lead to a relatively even spread of collateral across all Authority Set Validators.

**What This Looks Like In Practice**

To illustrate how it works, let’s look at a hypothetical Validator set of just 7 nodes, labelled A-G:

![Round 1 Auction Cycle Results](<../.gitbook/assets/Number 1 (1).png>)

The figure above showcases a system where all nodes which successfully bid, are paid the same in rewards, irrespective of the size of their stake. So long as they have a Validator slot, the Validator gains no extra reward for staking more. However, that doesn’t mean there are no advantages: Validators that have a higher stake are _much less likely_ to be outbid in the next auction cycle. Let’s explore that by looking at a following auction cycle. For the sake of the example, we will assume that all returns from the last auction cycle will be reinvested into the next cycle, and not unlocked:

![Round 2 Auction Cycle Results](<../.gitbook/assets/Number 2 (1).png>)

The figure above showcases that during the last Validator epoch, the active Validators earned 4k $FLIP each. However, Validator F added another 7k $FLIP to their bid, jumping up to Slot 5 and knocking out Validator E, who didn’t top up their bid. Through the process, the Minimum Active Bid has **risen** from 120k $FLIP to 125k $FLIP with just a _single new bid_ and a single node slot being cycled.

This achieves a range of the stated goals:

* **Maximise the collateral locked in Validator nodes** — by _automatically_ including the rewards of Validators into the next auction cycle, there is a natural tendency for Validators who do not unstake rewards to drive up the Minimum Active Bid each cycle.
* **Encourage a relatively even distribution of collateral across the Validator network** — Because all Validators earn the same rewards, there is no inherent incentive to stake a large number of tokens into a single Validator. Validators at the very top of the ladder may have enough to unstake what they don’t need and use it to bid for a second slot, which levels out the average distribution of bids.
* **Minimise active involvement in the auction process for adequately collateralised nodes (reduce the mental bandwidth required to run a node)** — Most Validators _won’t even have to pay attention_ to the process if they do not withdraw their rewards. Most Validators will not need to execute more than one on-chain staking transaction. This includes prospective bidders who should also stake whatever amount they can afford straight away.
* **Encourage a stable Validator set without excluding new entrants** — The system, while permissionless, **does** favour those that haven’t been previously slashed and keep their rewards staked. This helps foster a stable and secure network that makes it challenging for malicious actors to try and outbid large chunks of the slots, whilst still remaining competitive on the lower end of the Validator set.
* **Minimise gas costs associated with participating in the process** — Most Validators will not need to execute more than one on-chain staking transaction. This includes prospective bidders who should also stake whatever amount they can afford straight away.

**What Happens If I’m Unsuccessful?**

One of the downsides with this auction design is that it leaves would-be Validators with insufficient capital empty handed after each auction cycle. This is bad, as these prospective Validators are earning no rewards and thus have no incentive to maintain the Validator node they have spent time and energy setting up. The same goes for Validators which have been outbid. This means they’ll be less likely to stick around and bid again in the next auction, as they still have to pay for and maintain their infrastructure in the meantime.

Further to this, because an Emergency Rotation is always possible (if enough of the network goes offline during an Epoch), our design should make sure that there are always some extra nodes on standby ready to fill slots which are freed up by an emergency rotation scenario, or by otherwise successful bidders being offline during the Key Generation step in the process.

Thus, we need an incentive for these so-called ‘Backup Validators.’ By defining a set of nodes which are _not_ included in the Authority Set, but _are_ given a small reward for being around and staying alive, we ensure that getting outbid, being offline, or failing to bid enough to join the set could still be a profitable exercise for these operators. It also ensures that there’s always a set of online nodes monitoring the state chain and ready to participate.

### Backup Validators

Instead of paying an equal reward to the Backup Validators, a fixed reward is distributed proportionally to Backup Validators based on their stake size. This is because we want to incentivise these Backup Validators to have the highest amount they can in case of an Emergency Rotation, in which the highest bidding backup nodes would be included first, and also to incentivise the nodes to hold onto their stakes and await the next auction.

For Backup Validators, we also allow bids to be placed outside of the normal auction cycle, and immediately reflect increased bids for Backup Validators in the rewards they are paid. This provides a **direct and immediate incentive to stake as much as possible as soon as possible**, both increasing total bidding and increasing the likelihood that these more active and collateralised Backup Validators will be included in the next set.

The rules for Backup Validators are as follows:

* A fixed reward of FLIP (much less than the Authroity Set reward) is allocated to the Backup Validators for a given Epoch.
* There is a limit on the number of Backup Validator slots - 1/3rd of the current Authority Set size. Any bidding node outside this limit will not be a Backup Validator.
* So long as Backup Validator remains Qualified (including being Online), rewards will be paid to it based on their stake, proportional to their share of staked FLIP in the total number of FLIP staked in Backup Validators (It should be noted that individual Backup Validators will never earn more than Authority Validators).
* Any Backup Validator that goes offline will no longer be Qualified, and therefore gives up a Backup slot to another online Bidding node. However, they can come back online at any time, and provided they are still bidding enough to win back their Backup Validator slot, can immediately begin earning rewards again after the first successful heartbeat interval.
* In an Emergency Rotation, only the top third of Backup Validators will be included in the Emergency Set. This is to prevent mass deregistration events allowing large numbers of low-collateral nodes to form a superminority in the vast majority of cases.

Going back to our example, with the Backup Validator system in place, our third round might look something like this:

![Round 3 Auction Cycle Results](<../.gitbook/assets/Number 3.png>)

As you can see, Validator E is earning enough rewards to stay close to the Minimum Active Bid. If one of the Authority Validators were to unstake or be deregistered, the Minimum Active Bid would drop to 124k $FLIP, if no new bids are placed at all.

The design addition of rewards for Backup Validators solves for the remaining problems with Chainflip’s SSOD Auctions, better addressing the key goals of the design and ensuring a more stable and redundant network composition, whilst encouraging competition even among participants bidding below the Minimum Active Bid threshold.

**In Summary**

Chainflip’s SSOD Auction system should produce a range of positive behaviour from Validators, with the effect of furthering the defined goals of the network. It provides a simple and effective framework for Validators to maintain long term positions within the network, whilst also naturally encouraging the reinvestment of rewards back into the Validator slots. It is fair and predictable to new entrants who, after the first few auction cycles, should be able to accurately estimate their performance in auction rounds before they start.

Coupled with additional rewards, the SSOD auction creates incentives for Backup Validators to remain active. This will help keep the minimum bid higher when other Validators drop out (or in the case of an Emergency Rotation), making for more sustainable collateralisation of the network.

Higher collateral means higher liquidity security. In turn, this maximises the number of assets that Chainflip can support and allows for better pricing for users. Better prices should mean more volume, and more volume means more destroyed FLIP. Destroying FLIP should serve to increase the overall collateralisation of the network. You can hopefully see that this has the potential to form a strong positive feedback loop under the right conditions.

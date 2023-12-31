# Egress: Broadcasting Funds

Registering external events into the protocol is one thing, but sending funds out of it is more complex.  Chainflip also has a generic process for creating, signing and broadcasting transactions out of the protocol: **Egress**. Some examples are:

1. Sending the output of a swap to a user's **Destination Address**
2. Sending assets to a liquidity provider that is **withdrawing their collateral**
3. Sending ERC-20 $FLIP funds from the `StateChainGateway` contract when a Validator or other State Chain account holder **redeems $FLIP from their State Chain account**
4. Rotating aggregate keys or sweeping funds in the Vaults

As with ingress, the process for egress differs slightly from case to case but shares a common process.

## Threshold Signing

Events on the State Chain may trigger an Egress process. Typically, those events will also include the complete information required to construct a valid transaction to be signed over, including the gas fee and limit. Wherever possible, egress processes will be batched together to save on gas fees and to minimise the number of signing ceremonies required to conduct Egress operations.&#x20;

A`ThresholdSignatureRequest`signals to the Authority Set that a transaction must be signed by the network. Upon receiving this event in a State Chain block, the Validators begin a ceremony off-chain in the Chainflip Engine.&#x20;

### **Threshold Signature Scheme (**TSS) Signing Ceremonies&#x20;

TSS signing in Chainflip is conducted using the [FROST multisig scheme](../components/frost-signature-scheme.md), which relies on Schnorr signatures for fast and scalable multi-party computation, allowing for a large set of signers. FROST allows Chainflip to secure all Vaults using the same Authority Set of 150 Validators, offering substantial benefits in terms of economic security and a simpler Vault management logic compared to other Cross-Chain Liquidity Networks.

If the ceremony fails, it will time out on the State Chain and will be automatically restarted. Once the signing ceremony has been successfully completed, the signed transaction is reported on the State Chain and can now be broadcast to the external blockchain.

## Broadcasting

The Validators will now deterministically nominate one of the Authorities to broadcast that signed transaction to the destination chain. Technically, in most networks, anyone can submit this transaction from any client, but as this is a programmatic system, one is chosen to do so to automate this flow for all users.

If the nominated Validator can't send the transaction for some reason (not enough gas, broken connection, etc.), they will report the failure on the State Chain and another broadcaster is selected.&#x20;

Otherwise, the network will wait for the broadcaster to submit the transaction externally, which will then be witnessed by the network. If the broadcast is witnessed by the network before the timeout, the broadcast is considered successful. If it is not witnessed by the end of the timeout period, the nominated Validator will be punished and a new Validator will be nominated. This will continue until the broadcast is successfully witnessed.

###

###

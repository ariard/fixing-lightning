## Thoughts on fixing Lightning

This is a 1st draft of fixing some major security weaknesses of
Lightning and improving its capital efficiency.

### Problem: Dynamic Feerates for Time-Sensitive Transactions

Currently, in face of mempool spikes the level of top feerates
to be included in the blocks can stay superior to the amount
a HTLC is worth, or even superior to the lightning node expected
yield.

### Solution: Feerate-Dependent timelocks

Introduce feerate-dependent timelocks, where a HTLC-timeout
kick-off of the timelock is delayed as long as the median
feerate of the last X blocks is under a target.

### Problem: Package Malleability

Currently, Lightning time-sensitive transactions are bumped
by external child-pay-for-parent, which will always open
the door to counterparty malleability, a.k.a replacement
cycling attacks.
 
### Solution: Feerate Malleable-Resistant Transaction

Ensure the Lightning time-sensitive transaction feerate
is always a monotonic counter towards increasing the
selection odds of its inclusion in a block. All feerates
contributed should come from a transaction input itself,
and this feerate scale up should be unilaterally adjuted
by a counterparty e.g SIGHASH_ANYAMOUNT.

### Problem: Dusty Outputs

Currently, dust HTLC outputs of which the on-chain feerate
spending cost is superior to their value are not added, or
limited after a given number. A high number of HTLC outputs
pollute the base-layer UTXO set.

### Solution: Feerate-Dependent Dust Aggregation

Introduce script-level aggregation of the dust outputs
under some level of median feerate reached. This can be
multi-staged transactions only roll out under at some
median feerate point.

### Problem: Lightning Capital Ineffiency

Currently, Lightning is capital inefficient as any channel
and HTLC routed over it has to be fully capitalized at a
100% level of amount. This is independent of the HTLCs
locks duration or topological position of the lightning
node in the lightning graph.

### Solution: Dissociate Contract Circuitry from Value Feeding

Introduce sighash malleability flags and immutable covenant
logic to allow any lightning counterparty to feed the value
to meet committed HTLCs at time of on-chain settlement, and
not at time of off-chain creation of the HTLCs. This solution
should be sublinear in the number of UTXOs consumed per channel
today.
 
## Sources

[0] https://www.mail-archive.com/bitcoin-dev@lists.linuxfoundation.org/msg13176.html

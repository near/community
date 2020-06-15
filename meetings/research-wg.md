# Tue June 9 2020

### Quick recap

We discussed two topics:

1. Michael presented a proposal to switch from a model (a) in which we assign seats to participants who staked, such that each seat is backed by an equal stake, and then uniformly sample block producers and chunk producers uniformly to a different model (b) in which there's no concept of a seat, and we use weighted sample to sample block producers. We discussed the implications for the system.

2. Then Eugene presented a problem with nonces that results in transactions dropped, and we discussed possible alternatives.

# Tue June 2 2020

### Quick recap

Alex S presented an overview of how syncing works today. The existing syncing has several problems:

1. The state machine is pretty complex;
2. Blocks are downloaded one by one today due to the necessity to download chunks before the block can be applied;
3. Headers are pretty large, and once the chain is sufficiently long, just storing the header can be very resource-consuming.

We discussed a new proposed approach, that leverages the changes in the protocol (in particular, the notion of finality) since the syncing code was introduced, has a significantly simple state machine, and addresses the issues above.

# Tue May 26 2020

### Quick recap:
Continued the discussion of the congestion attack. 

# Tue May 19 2020
### Proposed discussion topics:
* Receipt and transaction congestion attack both in multi-sharded and single-sharded system. Issue: https://github.com/nearprotocol/NEPs/issues/67

### Quick recap:
See https://github.com/nearprotocol/NEPs/issues/67 for the overview of what was dicussed re: congestion attack, and further discussion.

# Tue May 12 2020
### Proposed discussion topics:
* Upgradability;
* Non-increasing transaction nonces;

### Quick recap:
Only had time to discuss upgradability.
See https://github.com/nearprotocol/NEPs/pull/64

# Fri May 8 2020
### Proposed discussion topics:
* With the current shards assignment logic and implementation, a block producer with a large stake will end up assigned to all the (or most of the) shards, and will run all those shards on a single node. Assuming network or storage are the bottleneck, this will end up making the system as a whole still go with the speed of a single instance.
Figure out how to fix it. Possible ideas:  
  * Change the shards assignment so that each node has a single shard assigned to them;
  * Introduce a way to launch multiple nodes for a single validator key such that each gets assigned a subset of the shards.
* Chunk producers within a particular epoch take turns in each block. It is likely that scheduling each to produce more than one chunk before rotating has some advantages (e.g. fewer chunks will be skipped).
* Chunk inclusion in the blocks today assumes a synchronous network. Can we redesign it in such a way that the synchronous network assumption is lifted?

### Quick recap:
* For (1):
  * Discussed the idea of assigning shards to block producers, not to seats (or, equivalently, assign a particular shard not to a random subset of seats, but to all the seats that belong to a particular block producer).
  * Two concerns voiced by Bowen and Marcelo: a) some shards now have way more stake securing them, and b) if only one block producer ends up assigned to each shard (or if fewer block producers are assigned to the shard), that shard is way more likely to stall.
  * For (a), for as long as we only slash one seat-worth of stake for an invalid state transition, the fact that more stake is assigned to a particular shard is not as important, though as Marcelo pointed out adaptive corruption of some shards is now cheaper.
  * For (b), the assignment can be adjusted in such a way that the number of block producers in each shard is the same as with random assignment.
* For (2):
  * Discussed the idea of having each block producer producing multiple chunks (say 10) before rotating, but rotating immediately if they skip a beat;
  * One issue is that we now can’t know the particular block producer for a slot in the future, which makes it harder to validate a partial encoded chunk if it arrives early. One idea is to accept optimistically chunks from all block producers assigned to a shard, up to one per BP per height (as opposed to just one per height today);
  * Michael pointed out that if the problem is chunks distributing fast enough, making them distributed faster could be a better solution (e.g. by reducing how much we are sending, for example sending tx hashes instead of transactions, and relying on the gossip network to have the transactions distributed in parallel);
* For (3):
  *   Discussed the idea of making the blocks delayed by some function F(\delta H) where \delta H is the difference between the current height and the last height in which a chunk in a particular shard was included. If F is increasing, then a stalled shard can make the entire system slow down significantly. Instead the idea is to introduce some constant C, such that if \delta H is less than C, F is increasing, but if \delta H > C, F(\delta H) = 0. This way for some time we wait longer and longer for a chunk, but at some point give up completely to ever receive it. Illia suggested that in this case we should prepare the next set of block producers. An idea to immediately switch to the chunk producers in the next epoch (who by now likely have the state ready) to operate the shard was discussed.


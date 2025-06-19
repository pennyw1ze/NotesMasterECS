#### Transaction validation
Bitcoin transaction validation rule:
- **Syntax** check;
- Respect allowed range **value**;
- For each input, if the referenced **output exists** in any other transaction in the pool, the transaction must be rejected;
- For each input, if the referenced output transaction is a coinbase output, it must have at least COINBASE_MATURITY (100) confirmations;
- Reject if the sum of input values is less than sum of output values;
- For each input, the referenced output must exist and cannot already be spent;
- The unlocking scripts for each input must validate against the corresponding output locking scripts;

Coinbase transaction is the first transaction in a block, has TXID 0s and VOUT max and represent the generation of new coins given to the miners as a reward.
The block reward can be sent to multiple locations.
It's placed there by the miner when they construct their candidate block so they can **claim the block reward** (block subsidy + fees) if they are successful in mining the block.
The outputs from coinbase transactions are the **source of new bitcoins**.

#### Merkle tree
Already seen in advanced cryptography course.
This structure allows the miners to aggregate all the transactions in a single SHA256 string, by computing SHA256 of all the transactions and then aggregating the hashing until reaching the "root" of this tree.
In this way, to verify if a transaction is actually in a block, we will need only to hash the transaction and to provide the hashing of all the siblings in the path from the transaction (leaf) to the merke root, which is embedded in the block. So instead of providing all the transactions and computing $N$ hashes, we will just provide and compute $log(N)$ hashes and we decrease the cost of checking the transaction.
**PROs**:
- Efficiently verify the presence of a transaction in a block;
    
- Efficiently verify the integrity of a block (e.g. a transaction integrity);
    
- Limit the memory to use Bitcoin to support light clients (e.g. mobile devices);

#### Nodes
There are 3 types of nodes in the bitcoin blockchain:
- **Full nodes**: full nodes verifies and relays the transactions and the blocks. They has to independently validate the complete copy of the blockchain since they have to download the entire blockchain;
- **Light nodes**: they connects to full nodes to interact with the blockchain. It needs only the chain of the blocks headers to operate. Light nodes don't have to trust full nodes since they provide merkle proofs of the provided data. They cannot validate the entire blockchains, but can check the existance of a node, so they do not earn anything;
- **Client nodes**: relies on 3rd party host providing APIs to look into the blockchain.These clients connect to a remote node and **completely trust** its responses in a non-cryptographically-proven manner.

****
# Consensus

> **Goal**
> The goal of the consensus in the blockchain is to have the same blockchain on every node despite the retardered propagation of informations in different part of the globe.

**Time** and **order of events** are fundamental obstacles in a system of distributed computers that are spatially separated.

Some general results about consensus are:
**FLP**
Even a single faulty process makes it impossible to reach consensus among deterministic asynchronous processes.
To circumvent FLP, If no progress is being made on deciding the next value, wait a timeout, then start the steps all over again. (Paxos protocol approach).
This approach are not byzantine fault tough.
Permissionless and public blockchains must achieve consensus in the following settings:
- Asynchronous systems;
- Byzantine and crashing nodes;
Paxos can satisfy the first requirement but not the second one. 
How do we deal with byzantinte ?
If there are $f$ byzantine process, the consensus can be achieved only if there are $3f + 1$ total nodes (look at [[Dependable Distributed System Recap#Byzantine tolerant Consensus]]).
We adopt new comunication primitives:
● Every message that is sent is delivered correctly;
● The receiver of a message knows who sent it;
● The absence of a message can be detected;
● A loyal general's signature cannot be forged, and any alteration of the contents of his signed messages can be detected;
● Anyone can verify the authenticity of a general's signature;


How do we achieve consensus ?

Let's start talking about how consensus is actually achieved in the bitcoin blockchain.
1. All the mining nodes pull transactions when they are published and creates blocks;
	
2. The block must then be attached to the blockchain (Proof of work);
	
3. If 2 ore more miners achieve the result simultaniously, then more blocks are attached at the same time to the same block and the a fork is created in the blockchain;
	
4. At this point, new transactions will be generated and new miners will create new blocks. This new blocks will be attached to one of the 2 "new chain" that forked from the original one;
	
5. When one of the 2 "new chain" becames longer then the other one (this is proved that will happen), the shortes chain is discarded and all the transactions in that chain will be re-mined into new blocks;

Since the lenght of the forked chain is not the same to everybody and forks could grow in different way in different machines around the world, a timer is added to slow down the growth of the blockchain.
This timer is the proof of work.

### Proof of Work
The proof of work consists in a complicated math puzzle that is slow to be solved for every machine and requires enough computational power to solve the challenge.
Nowadays a bitcoin puzzle requires 10 minutes circa.
Who solves the puzzle becomes the leader and can attach the next block to the blockchain.
##### The challenge
The challenge consists in reversing an hashing function.
Since hashing functions are hard to invert, the online chance for the miner is to bruteforce the hashing function. This require a lot of computational power which is not easy to accumulate. The challenge usually consists in finding the pre-image of the hashing function such that the image has a certain amount of zeros at the beginning. This challenge is post-quantum secure.

We still may have 2 simultanious winners to the challenge, and thus a fork is created.
Clearly the chain will not grow so fast since at least 7/8 minutes are required for the challenge to be solved, so it will be easy to detect the longest chain for every node in the chain.

There are a lot of controversial about the sustainability of the proof of work model expecially about the environmental point of view. A lot of work goes lost, wasting time and energy.

If a hacker is able to secure control of more than half of the network (51%), they are able to alter a blockchain and manipulate transactions to steal from the network. In blockchain, the more nodes, the more security.


### Proof of Stake
The idea is that if we can fairly reduce the number of participants to the consensus we can speed up the process.
We will do a weighted lottery where participants will place stake that they hold to participate to the lottery and the probability to win the lottery will be proportioned to the stake placed. If the validator choosen in the lottery is malicious and tries to validate a malicious block, his stake will be sliced an he will loose money. There is a minimum and a maximum amount of eth with which the participants can join the lottery with their stake.
There is a set of **active validators**, and 128 between them are randomly choosen to validate the proposed block. The algorithm is called randao.

How does the block production works in the PoS architecture?
An epoch is created, which last around 6.4 minutes.
In an epoch, 32 blocks are proposed, circa 12 second per slot, which correspond to a block.
How does the process works?
1.  A random validator proposes his block for a certain slot (some slots may be empty due to validator missing);
	
2. There is a pseudo-random process to select the commettee memebers, at least 128, that will create attestation to vote for a block. Attestations are weighted on the stake. Both validators and proposers are randomly picked trough the RANDAO algorithm;
	
3. If more then 2/3 of the committee validates the proposed block, it is attached to the beacon chain;
	
4. Validators also police each other and are rewarded for reporting other validators that make conflicting votes, or propose multiple blocks;


### RANDAO
RANDAO is a decentralized algorithm designed to generate random numbers in a decentralized way. The idea is to combine different inputs from a large group of people instead of simply trusting a person.

If we select a fixed committee we could use BFT (Byzantine Fault Tolerant) algorithms to achieve consensus.

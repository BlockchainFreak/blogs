# Scaling Blockchains with Rollups
## Background
  While Blockchain are designed to be decentralized and highly Secure, scalability is necessary for mass adoption. Each block in a blockchain stores some data which is capped by what we call the block size. A simple solution would be to increase the block size by 10 times which would result in blockchain that supports 10 times more transaction per second that it did before with a lesser transaction fee. Sounds Good. Right! However, the nodes now must process 10 times more data in each block at the same time as before. This implies that a node would have to spend more computational resources to keep up with the other nodes. Theoretically, you can pay them more mining rewards to compensate and hyperinflate the blockchain native currency. Don’t even talk about the massive carbon footprint. But this also means that some weak nodes will be ineligible to process blocks yielding in less nodes participating in a system, which in turn makes the blockchain less decentralized and less secure.
  
  The founder of Ethereum, Vitalik Buterin, coined a term Blockchain Trilemma to explain this. The Blockchain Trilemma states that a blockchain has to compromise in any one the three: Scalability, Security, and Decentralization to maximize the other two. While it is easy to understand the concept of scalability in terms of transactions per second, I found security and decentralization such entangled and abstract concepts when I got into the blockchain space. For Simplicity I would state them as:
1. Scalability = rate of transactions (in term of on chain data and on-chain computation)
2. Security = the mathematical complexity of consensus mechanism to process a particular size of data.
3. Decentralization = number of nodes participating in the network.

![](https://learn.swyftx.com/wp-content/uploads/2022/03/Figure1-1.png)

  The method of 10x scalability I just mentioned does not make a blockchain truly scalable. While the transactions per seconds increase linearly with the increase of block size, either security or decentralization must be compromised exponentially. Additionally, increased number of transaction and lesser fee generates induced demand anyways. Now, we are standing at from where we began with less security and centralization. Recursive scaling of blockchain in this naive way will make the blockchain useless. We have now lost the essence of security and decentralization which blockchains were designed for in the first place. 
## Effective Scaling Solutions
  There are numerous scaling solutions. We will focus on two main Rollups: zero knowledge Rollups and Optimistic Rollups. The basic principle on which rollups work is suppressing the data by doing off-chain computations and submitting a compressed version on the chain.
### Optimistic Rollups:
  The way optimistic rollups works is by bundling many transactions into one mega transaction and submitting it (usually to a smart contract) on the main chain which acts as a middle ware. However, the smart contract does not verify the rollups and assumes them to be valid by default as the name “optimistic” infers. However, anyone on the chain can challenge a particular rollup to be fraudulent. Only then, the smart contract will run transactions on chain to verify the validity of rollup. We know that on chain calculations are expensive and our smart contract need funds (gas) to verify the validity of rollup. This raises two contrasting but important concerns:
1.	A malicious node can false challenge every rollup to be fraudulent resulting in dozens of expensive calculations. The rollup was introduced to prevent this in the first place.
2.	Why would someone hunt for fraudulent transactions and challenge them if it does not incentivize them.

The solution to these questions is not technically complex, rather it involves principles of game theory.

  Because optimistic rollups have a waiting time in which they can be proved fraudulent, they must wait a week or two before the users can withdraw their funds on the main chain. During this period, the asserter must lock some of their funds in the contract as they submit rollups. Conversely, the challenger node must also lock their funds in the contract while challenging a particular rollup. The verifier contract runs the computation to verify the rollup. In case the rollup turns out to be fraudulent, the asserter funds are used to compensate for the gas that was used in on-chain computation and the rest is transferred to the challenger node incentivizing the challenger and penalizing the asserter who submitted the invalid transaction (rollup.) The same goes if the challenger was lying. It will be slashed of its funds.

  The asserter who submits the rollups gain fee from the whole process in general. Because they must lock up their funds, they would not purposely temper transactions as it might result in loss of funds. Similarly, challenging fraudulent nodes incentivized nodes to search for fraudulent transactions by brute forcing calculation off-chain to verify each rollup. This will be much cheaper and will prevent them for spamming fraud challenges to all rollups without verifying their fraudulence by themselves beforehand. The principle of game theory implies that each player will try to choose the most optimal condition for themselves. Therefore, both the asserter and challenger in rollups will be honest if they do not want to lose their funds. This contributes toward effective scalability of the blockchain without impacting the other counterparts: security and decentralization.

The only downside to Optimistic Rollups is that it takes a long waiting period for users before they can withdraw their funds on the main chain.

### ZK Rollups:
  ZK Rollups also bundles hundreds of transactions into a single transaction, but they use the zero-knowledge cryptography and submit the proof on-chain. To simply understand how zero knowledge proofs work, lets consider an example. Let’s say Bob wants to know that if Alice knows the data that hashes to 0xfd21. We know that hash algorithms are one way i.e., they can’t be reversed. The naive way is that Alice shares their data to Bob so he can hash the data and check if it is equal to 0xfd21. However, this involves the sharing of data by Alice which may be sensitive. Whereas, with the zero knowledge Alice can prove to bob that she knows the data without sharing the data. The Bob will initiate a function generateKeys(C, lambda) where c and lambda can be arbitrary seeding variables. This function returns a proving key (Pk) and verifying key (Vk). Bob shares the keys with Alice. Alice can run another function generateProof(Pk, data) which would return a zero knowledge proof. She shares the proof to Bob and he can run a function verifyProof(Proof, Vk, targetHash). If the verify function returns true, this implies that Alice knows the data which hashed to the targetHash. Bob does not share the seeding variables (C and lambda) to Alice otherwise she can create fake proofs. The cryptographic implementation of these functions is extremely complicated, but the general concept is easier to understand.
```typescript
const data = '*****' //only Alice knows this
const targetHash = '0xfd21'

const hash = Hash(data) //'0xfd21' 
/* Bob wants to know that alice
knows the key whose hash is equal to 0xfd21
*/

// Used by Bob to create Seed Variables
const [C, lambda] = randomSeed()

const [provingKey, VerifyingKey] = generateKeys(C, lambda)

//Bob passes Proving key and Verifying key to Alice

// ALice uses Proving Key to generate proof
const proof = generateProof(data, provingKey)

// Alice submits proof to Bob
// Bob verifies the proof

const isProofValid: Boolean = verify(data, verifyingKey, targetHash)
isProofValid ? "Alice knows the answer" : "Alice does not know"
```
  Woah! I just uttered so much gibberish just so that Alice can prove to Bob a simple piece of data without revealing the data. I won’t apologize as the zero knowledge proofs are complicated in nature. This was a general example to simplify the concept but how all of this relate to use of ZK Rollups in blockchains? Simple transactions in which users have to send some funds to other account requires the user to reveal their balance and the transfer amount to prove that they have enough funds to transfer {balance > transfer amount}. What if we could use to zero knowledge cryptography to prove that the sender has more balance that the transaction value without revealing either of them. Although, it seems utopic. Zero knowledge accomplishes this in a very sophisticated manner. Effectively implemented zero knowledge cryptographic can give robust functions that ensures creating a fake proof is practically impossible. 

  The disadvantage of ZK rollups is that it is a fairly new technology and such useful applications of it were hard to imagine just a few years before. Very few people understand it. It requires a lot of cryptographic knowledge and effective implementation (one that is computational less expensive) for practical usage in blockchain. Consequently, there is scarcity of such developers/cryptographers. Furthermore, the blockchain team needs a lot of funding to hire such developers. 

## Conclusion
  I believe the optimistic rollups are good for the time being. But sooner or later, they will be replaced by ZK rollups. It is because the zero knowledge is a new and complicated technology, hence, expensive. For one, it’s a great way to make shit loads of money by monetizing this value if they can provide. Let alone the gratification that comes with it. I feel intrigued by this idea and being able to a part of this transition.

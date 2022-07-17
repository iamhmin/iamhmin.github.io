---
layout: single
title: "[BlockChain] Definition and Types of Consensus Mechanisms"
categories : blockchain-basic
tag : [Consensus Mechanisms, bitcoin, PoS, PoW, DPoS]
author_profile: true
sidebar_main: true
toc: true
toc_label: Index
toc_sticky: true
---

## 1. Definition of Consensus Mechanisms
> Systems of agreement that  **validate the authenticity of transactions** and **maintain the security of the blockchain**. 
It's about how all the stakeholders in a decentralized network agree on the validity of shared data (typically some kind of transaction, but not always) and secure that data on the blockchain. It influences how transactions are verified, how much energy is used, network fees, transaction speed, and other details for the currency and network applications.

## 2. Four Major Types of Consensus Mechanisms
There’s a number of consensus mechanisms but I will be covering only four major mechanisms.

### 2-1. Proof-of-Work (PoW)
With proof-of-work, miners(computers that validate new transactions on most blockchains) compete to validate the next transaction block. When miners solve the computational puzzle, their block is considered valid and added to all copies of the blockchain, thus achieving consensus. **As a reward for their efforts(work)**, miners are granted a block reward in the form of coin.
- pros : a high degree of trusts
- cons : energy intensive

### 2-2. Proof-of-stake (PoS)
Those with the largest holding of the network’s currency validate new blocks. This enables faster and lower-cost transactions. 
the PoS mechanism **randomly** chooses a validator to validate blocks of data where the cost of an intended malicious error is greater than the block reward. **PoS algorithms** use several methods to select who will validate the next block.

1. **The size of the stake**: the more tokens staked, the higher the chance of being chosen to validate
2. **The age of the tokens staked**: the longer the tokens have been unspent, the higher the chance of being chosen to validate (once that stake is used to verify a block, its age is reset to zero)
3. **Random selection**: the lowest hash value (the string of numbers created by a hashing algorithm to represent data) combined with the largest stake is chosen to validate the next block

Here is how to keep validators from acting maliciously or adding fraudulent transactions into the block: the total value of tokens staked needs to exceed the transaction fees earned. If fraudulent activity is detected, the validator loses their stake and will not be allowed to participate in the validation process in the future. **This is why larger stakes are favored, since the validator stands to lose more from acting maliciously.** This system helps create trust.

- pros 
1. significantly less computation required compared to PoW
2. The pointlessness of a "51% attack"

- cons 
1. Nothing at Stake Problem
2. Initial Distribution Problem
3. The long range attack problem

### 2-3. Delegated Proof-of-Stake (DPoS) 
DPoS is a variation of PoS in which users who stake their coins can vote on the number of delegates to create new blocks. Delegates are also called **witnesses or block producers**. Users vote on delegates by **pooling your tokens into a staking pool and linking those to a particular delegate.** You do not physically transfer your tokens to another wallet, but instead utilize a staking service provider to stake your tokens in a staking pool.

Elected delegates receive the transaction fees from the validated block, and that reward is then shared with users who pooled their tokens in the successful delegate’s pool. The rewards are shared based on each user’s stake; **so if your stake represents 5% of the total staking balance, you would receive 5% of the block reward.**

Block producers — delegates — can be voted out **at any time**, so the potential for loss of income and reputation provides a hedge against bad behavior.

- pros 
1. With a limited number of validators, DPoS allows the network to reach consensus more quickly than PoS does
2. High performance and better scalability with a good TPS(transactions per second)
3. Malevolent witnesses or delegates can be voted out almost **real-time**
4. Low Transactions fees
5. Energy-efficient and cost-efficient and it can be cloud-based
6. Semi-decentralized with a more robust governance system

- cons 
1. Possibility of centralization when big “whales” gain much power in the network
2. More susceptible to a 51% attack
3. Stakeholders with small stakes may not have an incentive to vote

### 2-4. Proof-of-Authority
Proof-of-authority is used mainly by private companies or organizations that use blocks created by vetted sources who have special permissions to access the network. Assurances are based on **reputation and authority** rather than public consensus as with other mechanisms.

## 3. Worth Remembering
- Ethereum is moving away from proof-of-work and migrating toward a proof-of-stake consensus mechanism in its Ethereum 2.0 update.
- Nothing at Stake Problem : In the event of a fork, whether the fork is accidental or a malicious attempt to rewrite history and reverse a transaction, the optimal strategy for any miner is to mine on every chain, so that the miner gets their reward no matter which fork wins.


<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-6596953683217931"
     crossorigin="anonymous"></script>
<ins class="adsbygoogle"
     style="display:block"
     data-ad-format="fluid"
     data-ad-layout-key="-i5+5+1+2-3"
     data-ad-client="ca-pub-6596953683217931"
     data-ad-slot="2948544388"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

## Reference
[What Is a Consensus Mechanism?](https://www.thebalance.com/what-is-a-consensus-mechanism-5211399#toc-pros-and-cons-of-consensus-mechanisms) 

[What Are Proof of Stake and Delegated Proof of Stake?](https://www.gemini.com/cryptopedia/proof-of-stake-delegated-pos-dpos)

[Delegated Proof of Stake or DPoS](https://levelup.gitconnected.com/delegated-proof-of-stake-of-dpos-f60a27c16341)




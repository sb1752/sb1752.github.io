---
title: Trusting Bitcoin's Scarcity
summary: How can we trust that the total supply of Bitcoin will be capped at 21 million? Let's discuss how Bitcoin works and verify it for ourselves.
image: get-block-subsidy.png
layout: post
date: 2022-01-25
updated: 2023-12-23
---

How can we trust that the total supply of Bitcoin is actually capped at 21 million? Can’t the developers simply change the code? What if Bitcoin is "*hacked*"? Who even reads the algorithms? And how would you know if it did change? Bitcoin’s core ethos encourages this kind of skeptical thinking. A common saying in the community is *“Don’t trust. Verify.*” So let’s dive into this and develop a deeper understanding. I'll first address some misconceptions from a non-technical perspective and then dive into the source code and show where this 21 million number comes from.

[*Jump ahead to the technical explanation below* &#x2193;](#verifying-bitcoins-supply)

In my discussions with non-technical people I have found that there are two fundamental misconceptions. The first is not understanding or fully appreciating what it means for code to be open source. The second is not knowing the difference between a client-server model and a distributed peer-to-peer network. 

When we say Bitcoin is open-source, this means that all the code - which includes calculation of Bitcoin’s supply - is written, maintained and tested in the public, available for anyone to see. Everyone has access to the codebase. Of course, this doesn’t mean everyone knows how to read it. A certain amount of programming knowledge is required. But learning enough to follow along, especially just the critical parts of the codebase, is actually not that hard as I’ll show at the bottom of this post. One can also pay attention to discussions among developers in the community publicly debating the real-world implications of major code changes.

For those not in the software industry, the concept of open-source is fairly radical. Almost all the proprietary software of every major technology company, including companies like Google and Amazon, are built on top of and rely heavily on free, open source software. The idea that such advanced, sophisticated software is developed completely in the open primarily through the volunteer efforts of developers without an immediate profit incentive is fairly unintuitive. This in itself is one of first hurdles to understanding how Bitcoin works and how its code can be “trusted.”

However, if the concept of open source is understood and appreciated, there may still be doubts about whether developers can change the code. In this conception, non-technical "users" are subject to the whims of a much smaller group of technical developers that can make changes to the codebase. Or perhaps the code can be hacked and changed without the core developer team’s consent, again forcing “users” to abide by the new changes. The code may be transparent and publicly visible, but can it not still be changed unilaterally by developers, those <a href="https://decrypt.co/76997/elizabeth-warren-crypto-big-banks-shadowy-super-coders?utm_source=twitter&utm_medium=social&utm_campaign=auto" target="_blank">shadowy, super coders</a>?

The notion of “users” here is a faulty mental model. It is understandable since that is how people interact with software today. For example, a user may have an Instagram account. When a developer makes a change to the code, perhaps updating the color scheme and layout, the user simply signs in and see the changes. They are esentially forced into the new experience without giving their consent. This is an example of the client-server model. The user’s browser or phone is the client and that client requests and accesses data from the company's servers, which limits what the client has access to. The client does not know what data the server will return or how that code is implemented or what data is collected from the client, etc.

Bitcoin, however, is not based on a client-server model. It’s a peer to peer distributed network. Instead of clients and centralized servers, there are only independent nodes. There is no central server where data is retrieved from. Instead, every node runs the software that is written by Bitcoin developers and therefore every node has full access to the code. And the data - which in this case is the entire blockchain of transactions - is stored separately on each individual node. In other words, every node has a copy of the complete codebase and the full blockchain data.

The nodes in aggregate form a peer to peer network. If a code update is made that is not supported by the network participants, node operators can choose not to run that software update on their node. If enough participants reject the update, the network continues to function as it did before, only accepting software updates that nodes agree to. **Bitcoin’s rules therefore are determined by the consensus of a globally distributed network of nodes**. No one individual entity, neither the developers nor the miners, can make any changes unilaterally. This is what Bitcoiners mean when we say it is decentralized. The network functions according to the demands of the network in totality. There are no rulers.

This leaves one more question. Can the consensus change? What if the distributed network of nodes decide to accept a change to the 21 million supply cap? It’s possible, but there is no reason to believe this will happen anytime soon for two reasons. One is ideological. People, such as myself, participate in Bitcoin because we align with the vision of a disinflationary monetary system as a superior alternative to the inflationary fiat system that exists today. The other reason has to do with self-interest. Bitcoiners are not incentivized to inflate away their own wealth by distributing more Bitcoin.

Today, there are <a href="https://bitnodes.io/" target="_blank">thousands of Bitcoin full node operators</a> all over the world. The network is permission-less and therefore anyone can join and run a node. There are many services such as <a href="https://getumbrel.com/" target="_blank">Umbrel</a> that make running a full node fairly simple. As a full node operator myself, I take it as my responsibility to pay attention to any code changes that materially impact the direction and goals of Bitcoin. You can be sure I’m paying attention to something as critical as the total supply. So, as an exercise to clear up any lingering doubts as well as empower you to confirm this for yourself, let’s take a look at the Bitcoin core codebase and verify the 21 million supply cap.

---

## VERIFYING BITCOIN'S SUPPLY

*Technical Deep Dive*

The first thing to point out is that this 21 million number is not actually enforced anywhere in the code. It is used as a sanity check in parts of the codebase, but the code does not actually enforce the hard cap at this level. What is enforced as a consensus rule is how much new Bitcoin is issued and added to the network in every block. As the number of new Bitcoin issued decreases over time and eventually reaches 0, the 21 million number is *derived* based on the accumulation of all Bitcoin issued over time. In fact, the total supply of Bitcoin is not *exactly* 21 million. The real number comes out to slightly less than that at 20,999,999.9769 BTC.

As a refresher, Bitcoin transactions are picked up and aggregated by a miner. Roughly every 10 minutes, transactions are “mined” into a collection called a block. Blocks are then chained together forming the blockchain, which acts as the digital ledger and final record of account. New Bitcoin is issued every time a block is mined as an incentive to the miner. This is known as the block subsidy and is how new Bitcoin enters the system.

Remember, Bitcoin is *disinflationary* meaning new Bitcoin periodically enters the system but that new amount decreases over time. Bitcoin is programmed such that every 210,000 blocks, the amount of Bitcoin issued as an incentive to miners is halved. This is how the Bitcoin issued decreases over time. 

Since a block is mined roughly every 10 minutes, 210,000 blocks equates to about four years (6 blocks an hour * 24 hours a day * 365 days a year * 4 years = roughly 210,000). Therefore every four years, Bitcoin’s block subsidy is halved. In the first four years of operation, the amount of Bitcoin issued to miners in each block was 50. Today, the amount of Bitcoin issued per block is 6.25. It went from 50 to 25 to 12.5 to 6.25. We are in the third halving cycle. After the 32nd halving, the Bitcoin reward goes to zero. After 32 halvings, the amount of the Bitcoin subsidy goes below a Satoshi, which is the smallest denomination. Therefore, no more Bitcoin is issued after that point.

With this in mind, let's take a look at the `GetBlockSubsidy` function in Bitcoin's code which can be found here: <a href="https://github.com/bitcoin/bitcoin/blob/master/src/validation.cpp#L1668" target="_blank">https://github.com/bitcoin/bitcoin/blob/master/src/validation.cpp#L1668</a>.

~~~c++
CAmount GetBlockSubsidy(int nHeight, const Consensus::Params& consensusParams)
{
    int halvings = nHeight / consensusParams.nSubsidyHalvingInterval;
    // Force block reward to zero when right shift is undefined.
    if (halvings >= 64)
        return 0;

    CAmount nSubsidy = 50 * COIN;
    // Subsidy is cut in half every 210,000 blocks which will occur approximately every 4 years.
    nSubsidy >>= halvings;
    return nSubsidy;
}
~~~

We can see here that first we calculate the halvings by taking the `nheight` and dividing that by the `consensusParams.nSubsidyHalvingInterval`. The `nheight` refers to the number of the block at the top of the blockchain. Since every block increments this number by 1, this number effectively tells us how many blocks there are in total in the chain. The subsidy halving interval is a constant with a value that can be found <a href="https://github.com/bitcoin/bitcoin/blob/160d23677ad799cf9b493eaa923b2ac080c3fb8e/src/kernel/chainparams.cpp#L77" target="_blank">here</a>. This is the 210,000 blocks number we were talking about earlier.

~~~c++
consensus.nSubsidyHalvingInterval = 210000;
~~~

This division gives us the number of the halving cycle that we are currently on. For example, the current block height as of this writing is `822142`. Taking this number divided by `210000` gives us `3.915` which is rounded down to the nearest integer or `3`. In another words, this block is in the third halving cycle. 

Let's skip the next part checking whether the halvings is greater than 64. We'll come back to that. For now let's move on to this line:

~~~c++
CAmount nSubsidy = 50 * COIN;
~~~

This line sets up the *initial* subsidy at Bitcoin's origination, which as we know is 50 BTC. The subsidy, however, is calculated in Satoshis instead of whole Bitcoin. `COIN` is defined in <a href="https://github.com/bitcoin/bitcoin/blob/623745ca74cf3f54b474dac106f5802b7929503f/src/consensus/amount.h#L15" target="_blank">this file</a> and is defined as 100,000,000. A Satoshi is the smallest unit of 1 Bitcoin, which is comprised of 100,000,000 Satoshis. For example, if 1 Bitcoin today is worth $42,000, then 1 Satoshi is worth $0.00042. The community shorthand for this is Sats. So this line is simply translating the 50 Bitcoin to the Sats equivalent.

Finally, let's move on to the last line modifying the `nSubsidy` based on the halving period we're in.

~~~c++
nSubsidy >>= halvings;
~~~

You'll notice a unique operator here which you likely haven't seen before, `>>=`. This is known as a *bitwise operation*. I won't go into the details of how this works, but at a high level this operation converts the subsidy number to binary format and then does an operation in which it *"shifts"* bits by the number of halvings. This is the mathematical equivalent of dividing by the number 2 a certain number of times equal to the number of halvings. In essence, this operation is dividing the `nSubsidy` by 2^3, or 8. So we start with the initial block subsidy of 50 bitcoin and then divide by 2 for every halving cycle to calculate the current block subsidy. 50/8 = 6.25. The current Bitcoin subsidy for each block mined is 6.25.

Ok now that we understand this code a little better, where does the 21 million number come from? This is simply derived mathematically based on the sum of all block subsidies over time:

~~~
210,000*50 + 210,000*25 + 210,000*12.5 + … + 210,000* (50/(2^32))
~~~

Which is the equivalent of:
~~~
210,000 * ( 50 + 25 + 12.5 + 6.25 + … + (50/(2^32)) )
~~~

This can also be written in mathematical notation, which is an image you might have seen floating around on Bitcoin Twitter or Nostr. 
<figure>
<img src="{{site.url}}/assets/images/bitcoin-mathematical-notation.jpeg" width="100%">
<figcaption>Source: <a href="https://lookingglasseducation.com/the-bitcoin-supply-formula-explained/" target="_blank">Looking Glass</a></figcaption>
</figure>

So what does this equal? We can calculate it programmatically or by using a spreadsheet. If we do, we get the exact number, which is a number slightly less than 21 million. But we can also approximate it using a <a href="https://en.wikipedia.org/wiki/Geometric_series" target="_blank">geometric series</a>. The geometric series formula tells us that this is roughly the same as `210000 * ( a / (1-r) )` where `a` is the initial number (50) and r is the ratio (0.5 since it is being repeatedly halved). Therefore this can be written as `210000 * ( 50 / (1-0.5) )` or `210000 * 100`. And we get `21,000,000`! To reiterate, the real number is slightly less than this since we just did an approximation which assumes an infinite series.

Now, let's go back and revisit that check that we skipped above.
~~~c++
    if (halvings >= 64)
        return 0;
~~~

Here, the subsidy reward is being forced to 0 after the 64th halving. Why are we doing this? Isn't it already 0 after 32 halvings? The reason for this has to do with the implementation of the bitwise shift-by operation. After 64, shifting by 65 bits is the equivalent to shifting by 1 bit. What this means is that after around 250 years and after the network has no longer issued Bitcoin since the 32nd halving, it would begin issuing new Bitcoin again which would break the 21 million hard cap![^1]. So we have to force the reward to be 0 after that point. A fix for this was introduced in 2014 <a href="https://github.com/bitcoin/bitcoin/pull/3842" target="_blank">https://github.com/bitcoin/bitcoin/pull/3842</a>.

As a Bitcoin node operator, I pay close attention to any major changes to these numbers, formulas and files. These are known as "consensus-breaking changes" and are a pretty big deal and closely monitored by all participants in Bitcoin. So it's important that we're aware of them and whether they are being changed. 

Hopefully, you were able to follow along, have a better understanding of how Bitcoin works and can even verify the 21 million supply cap for yourself.

[^1]: <a href="https://twitter.com/aantonop/status/1259835712859451393" target="_blank">https://twitter.com/aantonop/status/1259835712859451393</a>

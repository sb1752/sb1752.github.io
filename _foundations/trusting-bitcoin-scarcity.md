---
title: Trusting Bitcoin's Scarcity
summary: How can we trust that the total supply of Bitcoin will be capped at 21 million? Let's discuss how Bitcoin works and verify it for ourselves.
image: get-block-subsidy.png
layout: post
date: 2022-01-25
---

How can we trust that the total supply of Bitcoin will be capped at 21 million? This comes up fairly often as a criticism from prominent businessmen, journalists and economists. Can’t the developers simply change it? Can’t it be hacked? Who even reads the algorithms? How would you know if it did change? Ironically, Bitcoin’s core ethos encourages this kind of skeptical thinking, at least when it comes from a place of genuine curiosity. A common saying in the community is “Don’t trust. Verify.” So let’s explore these questions further and provide you with the knowledge and tools to verify this for yourself.

In my discussions with people, I have found that there are two fundamental misconceptions that underlie this concern about scarcity. The first is misunderstanding the difference between closed source and open source code. The second is misunderstanding the difference between the client-server model and a distributed peer-to-peer network. Though people have varying degrees of understanding about how Bitcoin works, let’s start with the most basic misconception that I tend to see people have.

When we say Bitcoin is open-source, this means that all the code - including the calculation of Bitcoin’s supply - is written, maintained and tested in public, available for anyone to see. This means everyone has access to the codebase. This doesn’t mean everyone knows how to read it of course. A certain amount of programming knowledge is required. But learning enough to follow along, especially just the critical parts of the codebase, is actually not that hard as I’ll show at the bottom of this post. One can also pay attention to discussions among developers in the community publicly debating the real-world implications of major code changes.

If you're not in the software industry, you might find the concept of open source to be pretty radical. Almost all the proprietary software of every major technology company, including companies like Google and Amazon, are built on top of and rely heavily on free, open source software. The idea that such advanced, sophisticated software is developed completely in the open solely through the volunteer efforts of developers without any immediate or obvious profit incentive is fairly unintuitive. This runs counter to the way the world works in nearly every other industry. And yet, that is how much of software technology evolves today. This in itself is one of first hurdles to understanding how Bitcoin works and how its code can be “trusted.”

However, let’s say you understand that Bitcoin is open source and what the implications of this are. You may still have doubts, wondering if developers can just change the code, forcing “users” to simply adopt the new changes. Or perhaps the code can be hacked and changed without the core developer team’s consent, again forcing “users” to abide by the new changes. The code may be transparent and publicly visible, but can it not still be changed unilaterally by developers, those <a href="https://decrypt.co/76997/elizabeth-warren-crypto-big-banks-shadowy-super-coders?utm_source=twitter&utm_medium=social&utm_campaign=auto" target="_blank">shadowy, super coders</a>?

The notion of “users” here is a faulty mental model, but is perfectly understandable since that is how people interact with most software today. For example, you may have a user account on Instagram. When a developer makes a change to the code, perhaps updating the color scheme and layout, you simply sign in and see the changes. You are forced into the new experience without giving your consent. This is an example of the client-server model. The user’s browser or phone is the client and that client requests and accesses data from the company's servers, which limits what the client has access to. The client does not know what data the server will return or how that code is implemented or what data is collected from the client, etc.

Bitcoin, however, is not based on a client-server model. It’s a peer to peer distributed network. Instead of clients and centralized servers, there are only independent nodes. There is no central server where data is retrieved from. Instead, every node runs the software that is written by Bitcoin developers and therefore every node has full access to the code. And the data - which in this case is the entire blockchain of transactions - is stored separately on each individual node. In other words, every node has a copy of the complete codebase and the full blockchain data.

The nodes in aggregate form a peer to peer network. If a code update is made that is not supported by the network participants, node operators can choose not to run that software update on their node. If enough participants reject the update, the network continues to function as it did before, only accepting software updates that nodes agree to. **Bitcoin’s rules therefore are determined by the consensus of a globally distributed network of nodes**. No one individual entity, neither the developers nor the miners, can make any changes unilaterally. This is what Bitcoiners mean when we say it is decentralized. The network functions according to the demands of the network in totality. There are no rulers. This is, again, a very radical concept and nothing like it has ever existed before in history. 

This leaves one more question. Can the consensus change? What if the distributed network of nodes decide to accept a change to the 21 million supply cap? It’s possible, but there is no reason to believe this will happen anytime soon for two reasons. One is ideological. People, such as myself, participate in Bitcoin because we align with the vision of a disinflationary monetary system as a superior alternative to the inflationary fiat system that exists today. The other reason has to do with self-interest. Bitcoiners are not incentivized to inflate away their own wealth by distributing more Bitcoin.

Today, there are thousands of Bitcoin full node operators all over the world. The network is permission-less and therefore anyone can join and run a node. There are many services such as <a href="https://getumbrel.com/" target="_blank">Umbrel</a> that make running a full node fairly simple. As a full node operator myself, I take it as my responsibility to pay attention to any code changes that materially impact the direction and goals of Bitcoin. You can be sure I’m paying attention to something as critical as the total supply. So, as an exercise to clear up any lingering doubts as well as empower you to confirm this for yourself, let’s take a look at the Bitcoin core codebase and verify the 21 million supply cap.

---

### VERIFYING BITCOIN'S SUPPLY

<em>Note: This section is a bit dense and somewhat technical, but if you take the time to go through it, I think you'll find it rewarding.</em>

Something to mention before we dive in is that the 21 million number as the total supply is not actually enforced anywhere in the code. It is hardcoded as a sanity check in certain areas to ensure individual transactions cannot exceed that amount for example. But the total supply is derived based on the issuance of new Bitcoin in each new block. As a quick refresher, Bitcoin transactions are picked up and aggregated in a set of blocks. Roughly every 10 minutes, transactions are “mined” into a collection called a block. Blocks are then chained together forming the blockchain, which acts as the digital ledger and final record of account. New Bitcoin is issued every time a block is mined, as an incentive to the miner. This is known as the block subsidy and is how new Bitcoin enters the system[^1].

Remember, Bitcoin is disinflationary meaning new Bitcoin periodically enters the system but that new amount decreases over time. Bitcoin is programmed such that every 210,000 blocks, the amount of Bitcoin issued as an incentive to miners is halved. Since a block is mined roughly every 10 minutes, 210,000 blocks equates to about four years (6 blocks an hour * 24 hours a day * 365 days a year * 4 years = roughly 210,000). Therefore every four years, Bitcoin’s block subsidy is halved. In the first four years of operation, the amount of Bitcoin issued to miners in each block was 50. Today, the amount of Bitcoin issued per block is 6.25. It went from 50 to 25 to 12.5 to 6.25. We are in the third halving cycle. After the 32nd halving, the Bitcoin reward goes to zero[^2].

With this in mind, let's take a look at the `GetBlockSubsidy` function in Bitcoin's code which can be found here: <a href="https://github.com/bitcoin/bitcoin/blob/master/src/validation.cpp#L1401" target="_blank">https://github.com/bitcoin/bitcoin/blob/master/src/validation.cpp#L1401</a>.

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

We can see here that first we calculate the halvings by taking the `nheight` and dividing that by the `consensusParams.nSubsidyHalvingInterval`. The `nheight` refers to the number of the block at the top of the blockchain. Since every block increments this number by 1, this number effectively tells us how many blocks there are in total in the chain. The subsidy halving interval is a constant with a value that can be found here: <a href="https://github.com/bitcoin/bitcoin/blob/master/src/chainparams.cpp#L67" target="_blank">https://github.com/bitcoin/bitcoin/blob/master/src/chainparams.cpp#L67</a>. 

~~~c++
consensus.nSubsidyHalvingInterval = 210000;
~~~

This division gives us the number of the halving cycle that we are currently on. For example, the current block height as of this writing is `720406`. Taking this number divided by `210000` gives us `3.43` which is rounded down to the nearest integer or `3`. In another words, this block is in the third halving cycle. 

Let's skip the next part checking whether the halvings is greater than 64 for now and move on to this line:

~~~c++
CAmount nSubsidy = 50 * COIN;
~~~

The subsidy is calculated in Satoshis instead of whole Bitcoin. `COIN` is defined in <a href="https://github.com/bitcoin/bitcoin/blob/623745ca74cf3f54b474dac106f5802b7929503f/src/consensus/amount.h#L15" target="_blank">this file</a> and is defined as 100,000,000. A Satoshi is the smallest unit of 1 Bitcoin, which is comprised of 100,000,000 Satoshis. For example, if 1 Bitcoin today is worth $35,000, then 1 Satoshi is worth $0.00035. The community shorthand for this is Sats. 

Finally, let's move on to the last line modifying the `nSubsidy`.

~~~c++
nSubsidy >>= halvings;
~~~

You'll notice a unique operator here which you likely haven't seen before, `>>=`. This is known as a bitwise operation. This is a bit too technical for this blog post, but what this does is convert the subsidy number to binary format and then does an operation by which it "shifts" bits by the number of halvings. This is the mathematical equivalent of dividing by the number 2, three times. Or the same as dividing by 2^3 which is 8. Essentially we divide the initial block subsidy of 50 bitcoin by 2 for every halving cycle to calculate the current block subsidy. 50/8 = 6.25.

Ok now that we understand this code a little better, how do we derive the 21 million number? From here, it’s just math. The total amount of bitcoin is the sum of all block subsidies over time:

~~~
210,000*50 + 210,000*25 + 210,000*12.5 + … + 210,000* (50/(2^32))
~~~

Which is the equivalent of:
~~~
210,000 * ( 50 + 25 + 12.5 + 6.25 + … + (50/(2^32)) )
~~~

What does the set in parenthesis equal? We can calculate it programmatically or by using a spreadsheet. Or we can approximate it using a <a href="https://en.wikipedia.org/wiki/Geometric_series" target="_blank">geometric series</a>. The geometric series formula tells us that this is roughly the same as `a / (1-r)` where a is the initial number and r is the ratio (0.5). Therefore the set in parenthesis equals `50 / (1-0.5)` or `100`. And `100 * 210,000 = 21 million`! We’ve confirmed the supply cap! In actuality, the number is slightly less than this since we just did an approximation which assumes an infinite series.

Let's revisit that check that we skipped above.
~~~c++
    if (halvings >= 64)
        return 0;
~~~

Here, the subsidy reward is being forced to 0 after the 64th halving. The reason for this has to do with the implementation of the bitwise shift-by operation. After 64, shifting by 65 bits is the equivalent to shifting by 1 bit. A fix for this was introduced in 2014 <a href="https://github.com/bitcoin/bitcoin/pull/3842" target="_blank">https://github.com/bitcoin/bitcoin/pull/3842</a>. Before that fix, what this meant was that after around 250 years, the Bitcoin network would begin issuing new Bitcoin again which would break the 21 million hard cap![^3].

As a Bitcoin node operator, I am paying close attention to any changes to these numbers and formulas, such as the `nSubsidyHalvingInterval` or any change to the bit shift operation, or any change to this function that calculates the block subsidy. Now you can verify it for yourself as well. 

Hopefully, you were able to follow along, have a better understanding of how Bitcoin works and can even verify the 21 million supply cap for yourself. If this was helpful or confusing or if you have any questions, feel free to let me know on Twitter: <a href="https://twitter.com/shaanbatra" target="_blank">@shaanbatra</a>. 

[^1]: This is a very simplistic explanation of the mining process which is often misunderstood and considered unnecessary and even harmful to the environment. However, this process is in fact essential to the functioning of the Bitcoin network. Real-world energy consumption offers the perfect balance of security and decentralization required to ensure Bitcoin is a viable long-term store of value. I will be writing more about how the mining process works and why it is essential in another post. 

[^2]: After 32 halvings, the amount of the Bitcoin subsidy goes below a Satoshi, which is the smallest denomination. Therefore, no more Bitcoin is issued after that point. 

[^3]: <a href="https://twitter.com/aantonop/status/1259835712859451393" target="_blank">https://twitter.com/aantonop/status/1259835712859451393</a>
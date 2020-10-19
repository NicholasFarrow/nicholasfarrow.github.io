---
layout: posts
title: "Copy Trading the KuCoin Hacker for Easy Profit"
author_profile: true
last_modified_at: 2020-10-19T03:20:02-05:00
date: 2020-10-19
excerpt: "I spent two weeks copy trading the KuCoin hacker for absolute bank, high leverage ape style."
header:
  teaser: "/assets/images/kucoin/teaser2.png"
  og_image: "/assets/images/kucoin/teaser2.png"
  image: "/assets/images/kucoin/header.png"
  caption: "leveraged ape"
  show_overlay_excerpt: false

layout: single
classes: wide
author_profile: true
read_time: true
comments: # true
share: true
related: true
toc: false
toc_label: "Contents"
comments: true
---
On the 25th of September the cryptocurrency exchange KuCoin was hacked for over $200M worth of Bitcoin, Ethereum, and ERC-20 tokens. By gaining access to the private key of KuCoin's *hot wallet*, the wallet used for day-to-day withdrawals and transactions, the hacker was able to completely drain the funds into addresses they controlled. In this post I explain how noticing a pattern in the hacker's behaviour and a quick python script allowed me to earn some sweet satoshis from this unfortunate event.

Note: It appears the [has been caught](https://news.bitcoin.com/kucoin-ceo-says-exchange-hack-suspects-found-204-million-recovered/) since the time of writing this.

# Observing the Wild Hacker
Just one day after stealing the funds, the hacker began selling the >$100m stolen Ethereum ERC-20 tokens, starting with Ocean and Sythetix Network Token. Selling patterns emerged early on:

![first selling](/assets/images/kucoin/ocean.png)

Here we see the first instance of selling, the hacker withdraws a portion of the tokens from the main address ([0xeab...](https://etherscan.io/address/0xeb31973e0febf3e3d7058234a5ebbae1ab4b8c23#tokentxns)) to another address where they then do a test sell of 100 OCEAN on the Uniswap contract. Once they have confirmed the test sell was successful they continue to sell the rest of tokens. These test sells happened for a majority of the tokens the hacker sold and it gave me a few minutes edge on the market, foreseeing that a sale was about to occur.

Uniswap is a fully decentralised protocol for liquidity provision, i.e. facilitates trading/switching between Ethereum tokens, in this case the hacker was selling their tokens for ETH. As Uniswap is truly decentralised, censoring the hacker's trades is impossible. However, for some of the more centralised shitcoins, developers manually called functions within the Ethereum smart contracts in order to recover/freeze the tokens.

The hacker had **huge** quantities of tokens, often controlling 1-3% of a token's entire supply. In an attempt to not crash token prices by dumping huge quantities on thin orderbooks, the hacker would sell the stolen tokens in small batches after completing the test sell. This appeared to be the work of one person, selling semiregular batches of a token every 1-2 minutes; but sometimes with short breaks. Later on, you could actually observe the hacker getting less precise through their typos in transaction amounts, and they became increasingly distracted as the time gaps between sells grew.

![mana selling](/assets/images/kucoin/MANA.png)

Despite the small batch size of each sell (few thousand USD at a time), the price of the tokens dumped, and **dumped hard**. Below we see a price chart for DIA, where the hacker's batch selling caused a 16% crash.

![DIA chart](/assets/images/kucoin/DIA2.png)

*The red lines mark the period for which the hacker was selling DIA, note how the absence of the hacker selling also marks a bottom!*

Like DIA, many of the sold tokens had small market caps (~$10m) with tiny daily volumes (~$5,000) and the hacker was attempting to sell MILLIONS of dollars worth quickly. Naturally, the thin orderbooks for these tokens could not support this immense selling pressure and these tokens were now on a firesale or deservedly rekt depending on your opinion of their value.

# Copy Trading
Quickly noticing the selling and token dumping patterns, I wrote a [basic python script](/assets/addresswatch.py.txt) to monitor the hacker's main token address ([0xeab...](https://etherscan.io/address/0xeb31973e0febf3e3d7058234a5ebbae1ab4b8c23#tokentxns)) for the initial withdrawals to other addresses, from which the selling on Uniswap would then begin.

<audio controls>
    <source src="/assets/images/kucoin/gold_please.mp3" type="audio/mpeg">
</audio>
<audio controls>
    <source src="/assets/images/kucoin/ally.mp3" type="audio/mpeg">
</audio>

When I heard the alarm sounds (aoe2 taunt) I would run the script again on the selling address and listen to the selling begin.

## 4. Profit!!!!
In order to profit from this price action, I needed a way to short the tokens. Short-selling, where you borrow an asset to sell now and later rebuy lower for a profit (hopefully), is only offered on a few exchanges and with varying limitations. Initially, I attempted to short these tokens on Binance, but the leverage was often low (lots of margin collateral required ~30%) and position limits were small. For example, on Binance I could only borrow 240 DIA, roughly $300 USD, which only returned ~$50 on a 16% crash.

[FTX.com](https://ftx.com/#a=apePost) on the otherhand, offers short selling on margin with up to 101x leverage and a position limit of $25,000 on these small tokens. That means with less than a $250 deposit I could short $25,000 USD worth of tokens!! Only a handful of the tokens the hacker had stolen were listed on FTX, but I did manage to catch a couple of dumps with epic returns

![DMM chart 1](/assets/images/kucoin/DMMAPE1.png)

*Red arrows == ape in, blue arrows == ape out. Candles are 1 minute.*

My strategy became:
1. Hear alarm, wake up & run over to laptop.
2. Check the transaction to see what token is being moved. Then, check if the token trades on FTX.
3. If so, place a $25k reduce-only stop loss slightly above the current token price in order to manage downside risk.
4. Market sell roughly 1/3 of my desired position size.
5. Wait a few seconds, allowing for algos/bots to fill up the bid side of the orderbook again so that there is some decent depth for my subsequent trades to fill into.
6. Market sell again to increase my position size. Repeat until position is complete.
7. Grab a beer and watch my profit/loss climb to hundreds of percent.
8. Watch until the hacker has stops selling, and then market-buy close my position in a similar way to how I entered.

For two weeks I carried my laptop everywhere, even to a beach at one stage, connected to my mobile hotspot the script would alert me at any time of day. Sometimes the price would start dumping only a matter of seconds after I had opened my positions. Thus it was integral that I opened my position as soon as possible, regardless of small deviations in entry price. Me aping in & aping out is why you can see noticable slippage and a huge spike in volume, I'm the market now.

![DMM chart 2](/assets/images/kucoin/DMMAPE.png)

FTX is an awesome exchange with some great derivatives (elections, oil, gold!) and I would [highly recommend trying it out](https://ftx.com/#a=apePost) if you haven't already.

The hacker seems to have stopped selling for now, but this script and strategy will likely work in future hacks and in similar scenarios. This strategy had a 100% success rate and my stop-losses never even came close to being hit. I speculate that by using Uniswap, the hacker crashed these coins more than they would have on other exchanges. This is because Uniswap is an *automatic market maker* where [slippage is inherent to every trade](https://cointelegraph.com/explained/uniswap-and-automated-market-makers-explained). When the hacker dumped tokens, arbitrage bots would have bought the cheaper tokens on uniswap and sold on other exchanges (FTX) in order to make a profit on the difference, perhaps increasing the severity of of the crashes given this happened over and over again.

![trades](/assets/images/kucoin/wins.png)

# Is this legal/moral?
All this information is openly accessible on the Ethereum blockchain, therefore it does not meet the definition for front-running nor insider trading. The people on the other side of my trade were likely either:
* Turkey bots and algos that were not anticipating thanksgiving.
* Weak hand token 'holders' who panic dumped their investment. From the tokens I have checked, all have recovered from this short term crash.
At one point I joined one of the token's investing groupchat where I saw many people excited for the opportunity to to invest in the project at a discounted price (perhaps a cope but they would've made it if they bought the bottom).

I don't trade very often, but this was a very fortunate low-risk way to stack a little more bitcoin. If you found this useful (or perhaps even profitable!), please consider supporting me:

**BTC: bc1qxqf3w0hd2x0zuhs82utvxrk435yzlw05w9t87s**

[Sign up for FTX.com](https://ftx.com/#a=apePost) if you haven't already.

<!-- Initially routine, but started to switch it up and get lazy/distracted. Selling at an impossibly slow speed.
# Liquid Knives check the Prize
* How long until they started selling
* Single indivual selling shedule 
* What they sold, some tokens were frozen
* How they sold, test sells
PLOT IDEAS : sell times, tokens sold, withdrawals

# Token Dumping
* Uniswap slippage and subsequent dumping
* Arbitrage mechanism dumping prices
* Why they sold for ether

# Token Dumping
-->

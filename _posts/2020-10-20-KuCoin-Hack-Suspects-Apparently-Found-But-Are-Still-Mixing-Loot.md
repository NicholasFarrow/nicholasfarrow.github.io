---
layout: posts
title: "KuCoin Hackers Apparently Found, But They're Still Mixing Loot"
author_profile: true
last_modified_at: 2020-10-20T03:20:02-05:00
date: 2020-10-20
excerpt: "KuCoin CEO announced the hackers have been found, but you can see they're still mixing more than $2M USD  of stolen funds with Tornado Cash."
header:
  teaser: "/assets/images/kucoin/kucoinpost2header.png"
  og_image: "/assets/images/kucoin/kucoinpost2header.png"
  image: "/assets/images/kucoin/kucoinpost2head.png"
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
Eight days after the $200M KuCoin hack, the CEO Johnny Lyu announced that the suspects had been found:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">A quick update since my last livestream on Sep 30. <br><br>After a thorough investigation, we have found the suspects of the 9.26 <a href="https://twitter.com/hashtag/KuCoin?src=hash&amp;ref_src=twsrc%5Etfw">#KuCoin</a> Security Incident with substantial proof at hand. Law enforcement officials and police are officially involved to take action.</p>&mdash; lyu_johnny (@lyu_johnny) <a href="https://twitter.com/lyu_johnny/status/1312359615091277824?ref_src=twsrc%5Etfw">October 3, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

However over $1.3M USD (3,700 ETH) continues to be actively mixed using Tornado Cash, as recently as two hours ago:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">⚠ 100 <a href="https://twitter.com/hashtag/ETH?src=hash&amp;ref_src=twsrc%5Etfw">#ETH</a> (37,332 USD) of stolen funds transferred from Kucoin Hack 2020 to Tornado Cash<br><br>Tx: <a href="https://t.co/WlgR9VsBKf">https://t.co/WlgR9VsBKf</a></p>&mdash; Whale Alert (@whale_alert) <a href="https://twitter.com/whale_alert/status/1318104112660172800?ref_src=twsrc%5Etfw">October 19, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Note Lyu's use of plural *'suspects'*. Despite being over two weeks of law enforcement being *'officially involved to take action'*, it appears at least one hacker or accomplice is yet to be apprehended. This also raises some doubt as to whether **any** of the suspects have actually been identified or caught at all. While Lyu states there is *'substantial proof'* for action, there have been no news headlines or KuCoin statements since.

![mixed loot](/assets/images/kucoin/lootmix1.png)

*Stolen funds being actively mixed with Tornado cash, weeks after a statement saying suspects had been identified.*

Not only is a hacker attempting to mix this $1.3M USD, but by following the most recent transaction of 81 ETH to [0xf519...](https://etherscan.io/address/0xf519e276958c3ef2dffd9b6b2d87d26859526505), we see they are mixing another 3,235 ETH ($1.2M USD) through that address:

![mixed loot](/assets/images/kucoin/lootmix2.png)

By sending the 81 ETH change over to the other address where they are mixing funds, we can see that one person is likely mixing both the 3,700 + 3,252 ETH = 6952 ETH funds.

![tornado cash](/assets/images/kucoin/tornado.png)

Hopefully the KuCoin statement is true and the suspects can be apprehended soon.

## Mixing with Tornado Cash
Like Wasabi, the privacy-focused Bitcoin wallet, Tornado Cash uses the CoinJoin method to increase the privacy of transactions. This is achieved by combining or 'joining' multiple 100 ETH payments from multiple spenders into a single Ethereum transaction, in this case via an Ethereum smart contract. The large number of inputs, the anonymity set, makes it difficult to match each input with an identically sized output and thus mixing the funds.

Support me:

**BTC: bc1q536uh923zs2m2lnvyytvvt7nsg42xfeu2r7pt4**

**ETH: 0xC49413138C3E6A979dBb13666BC0172246f4c803**

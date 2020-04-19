---
layout: posts
title: "The Origin of GW190425"
author_profile: true
last_modified_at: 2020-04-19T03:20:02-05:00
date: 2020-04-19
header:
  teaser: "/assets/images/GWpreview.png"
  og_image: "/assets/images/GWprieview.png"
excerpt: "GW190425 is the second detection of gravitational waves originating from a double neutron star inspiral, and was the topic of my honours thesis and paper."
layout: single
classes: wide
author_profile: true
read_time: true
comments: # true
share: true
related: true
toc: true
toc_label: "Contents"
comments: true
---
Here I discuss GW190425, which was the topic of [honours thesis](/assets/thesis.pdf) and our team's [subsequent paper](https://arxiv.org/abs/2001.06492) that is currently awaiting publication.

# GW190425
On the 25th of April 2019, Advanced LIGO/Virgo made the second detection of gravitational waves (GW) originating from a double neutron star (DNS) inspiral. This gravitational wave, [GW190425](https://www.ligo.org/detections/GW190425.php), is particularly interesting as it originated from an enormous double neutron star system.

The total mass of GW190425 was inferred to be $$ 3.4^{+0.3}_{-0.1} M_{\odot} $$; that is 3.4 times the mass of our sun whilst each star is roughly just ~$$20$$km across! The total mass of GW190425 is inconsistent with other observed double neutron stars at a $$5\sigma$$ level, indicating that we may have just found a new population of neutron stars not previously observed.

## Gravitational Waves
Predicted by Albert Einstein in 1916, gravitational waves are radiated by accelerating objects. As a consequence, all orbits lose energy from the emission of this gravitational radiation, causing the distance and eccentricity of a binary system to decay over time. This leads to an **inspiral** where two compact objects (neutron stars or blackholes) become closer in an increasingly circular orbit. The closer the stars get, the faster their orbital velocities become and consequently the period of the orbit decreases, reflected in the gravitational waves as an increasing frequency. The crescendo of this orbital frequency is reflected in the gravitational wave frequency as a *chirp*. The *chirp* ends with a merger between the two compact objects, forming a supramassive neutron star or a black hole.

<iframe width="560" height="315" src="https://www.youtube.com/embed/yYCnp_42mgY" frameborder="0" allowfullscreen></iframe>
*Coalescing neutron stars in simulation of GW190425, **CoRe collaboration***


# 3.4 Times the Mass of our Sun?!
The observation of such a massive binary points us towards one of the most exciting questions in astrophysics: **how do stellar binaries form and evolve?**. Currently, there are two predominant formation scenarios for compact binaries, which not only includes DNS formation but also binary black holes and neutron star-black hole binaries.

In the *dynamic formation* scenario, two compact objects (neutron stars/black holes) form individually in a dense stellar environment such as a globular cluster, a collection of stars orbiting the core of a galaxy. These two compact objects are later brought together via dynamical interactions to form a binary.

In *isolated binary evolution*, two stars begin in an orbit where one of the stars begins to grow in size as it burns off the remainder of its hydrogen. This larger star may get too large and too close to the other star, causing the outer layer of the star to be gravitationally pulled onto the other star and forming a *common envelope* of hydrogen which surrounds both stars. This *mass transfer* continues until the hydrogen layer of the larger star has been stripped off, leaving a helium star. Sometime later, this helium star will undergo a supernova explosion, collapsing into a neutron star.

The implied merger rate from GW190425 is higher than we expect from the dynamical channel, and the total mass is difficult to explain through standard isolated binary evolution. Therefore neither scenario fits for explaining the origin of GW190425.


# Possible Explanation: Unstable Case BB Mass Transfer
![Panel AB](/assets/images/AB.png){: style="float: left; margin-right: 1em;"}
*Credit: Carl Knox*

Following standard isolated binary evolution our binary star system may now consist of a primary neutron star and a high-mass Helium rich giant companion (Panel A). Unstable case BB mass transfer may occur where Helium is pulled off the smaller star, forming a *common envelope* which now engulfs the neutron star and a remaining carbon-oxygen core (Panel C).

![Panel CD](/assets/images/CD.png){: style="float: right; margin-left: 1em;"}
Friction in the common envelope transfers angular momentum from the neutron star and carbon-oxygen core to the surrounding helium, eventually ejecting the envelope leaving a tight binary orbit with an orbital period of less than 1 hour (Panel D).


## Supernova Kicks
![Panel EF](/assets/images/EF.png){: style="float: left; margin-right: 1em;"}
Sometime later the carbon oxygen core undergoes a supernova explosion (Panel E), collapsing into a second neutron star. During the supernova, the asymmetric distribution of mass in the collapse gives the neutron star a *kick*. This kick gives the star a boost in a random direction of its orbit, leading to an elongated orbit with significant eccentricity. Supernova kicks are thought to disrupt many binaries, providing the star with an orbital velocity greater than its escape velocity.


# Explaining GW190425
Unstable case BB mass transfer is a great candidate for explaining the origin of GW190425 for several reasons:
1. Research from Ivanova et al. in 2003 showed that a heavy He-NS binary can survive unstable case BB mass transfer, with one of their simulated binaries retaining a $$ 3.01 M_{\odot} $$ helium star after the common envelope.
2. The short orbital period of less than one hour makes these binaries unlikely to be detected in radio pulsar surveys, the method through which DNS are commonly found, due to severe Doppler smearing. As well as a relatively short time to merger of less than 10 million years, which makes them unlikely to be detected before they merge when compared to other long-lived DNS.
3. Unstable case BB mass transfer binaries are thought to almost always survive the supernova kick due to their very tight orbit.

# Searching for Signs of Eccentricity
In [our recent paper](https://arxiv.org/abs/2001.06492), we propose that GW190425 may have formed as an isolated binary via unstable case BB mass transfer. Where the first common envelope left a binary consisting of a $$\sim1.4M_{\odot}$$ neutron star and a $$\sim4-5M_{\odot}$$ helium star with an orbital period of $$0.1-2$$ days. The unstable case BB mass transfer may have accreted $$\sim0.05-0.1M_{\odot}$$ of mass onto the first born neutron star during this second common envelope.

The second supernova ejected $$\sim1M_{\odot}$$ leaving a $$\sim(1.4+2.0)M_{\odot}$$ DNS, likely with significant eccentricity as a result of the *kick*. To test our hypothesis, we measured the eccentricity of GW190425 using the LIGO/Virgo GW data so that we could compare it to expected eccentricities of simulated unstable case BB binaries.

In my next post I will explain: some technical details of the method from Isobel Romero-Shaw, who led our team in measuring the eccentricity of GW190425; how we simulated eccentricities of unstable case BB mass transfer binaries; and finally our findings and some further prospects in measuring the eccentricity of merging neutron stars.

For more details and citations, see my [honours thesis](/assets/thesis.pdf) and [our paper](https://arxiv.org/abs/2001.06492) that is currently awaiting publication.

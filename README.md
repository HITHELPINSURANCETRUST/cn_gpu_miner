# JCE Cryptonote CPU+GPU Miner
The Fastest Cryptonote CPU Miner ever, plus some tastes of GPU!

BitcoinTalk Topic: https://bitcointalk.org/index.php?topic=3281187.0

:heavy_exclamation_mark: Security Alert! Some hackers pack a Trojan in a fake JCE release, complete with the doc. :heavy_exclamation_mark: That's usually a small .rar when official JCE are big .zip\
Download JCE only from this Github page.

Here's the GPU prototype version, which still offers the CPU mode, plus a OpenCL GPU mode.\
While targetting AMD cards, it may work on nVidia too, i cannot test since I own zero nVidia card. To be tested.

# Index

* [Speed](#speed)
* [Warming-up](#warming-up)
* [Fees](#fees)
* [Planned features](#planned-features)
* [Configuration](#configuration)
* [Troubleshooting](#troubleshooting)

## Speed

It's hard to compare GPU Miners. There are a lot of external parameters: the card itself, its memory, the biosmod, the drivers, the overclocking, the Power Limit...

However, according to the first feedbacks, here's the status:

* JCE is faster than any other miner on small RX cards (RX550 and RX560). They are my favorite cards, and the ones I use on mosyt of my rigs.
* JCE is always faster than the Wolf0-based miners (Stak, Xmrig, Gateless...). This is not fair since I can read their code and they cannot read mine, but it's a proof JCE is not a copy-paste of their code.
* JCE is surprisingly fast on Vega, reaching 2050+ on Vega 64 on CN-v7.
* JCE is close to SRB and Claymore otherwise, sometimes slightly above, sometime slighty below.
* JCE is disapointing on CN-Heavy, but that's the code I optimized the less so far.
* I got mixed results on HD7800. I measured higher hashrate than other miners on my rig, but got opposite comments from some users. To be tested.
* JCE is bad on small 1G cards compared to the legendary Claymore 9.7, but this miner is no longer supported.

The CPU part of JCE-GPU is the exact same than the CPU-only version.

## Warming-up

JCE lets the OpenCL driver allocate computing power progressively, and does not push the card at max immediately.\
**It starts at ~80% speed and grows up to 100% in about 5 minutes.** So let the miner warm up before comparing its speed to other miners.

## Fees

Current fees are:
* 0.9% on the GPUs
* 1.5% on the AES CPUs
* 3.0% on the non-AES CPUs

If you mix CPU and GPU, fees are adjusted proportionally.

## Planned features

JCE GPU is still in very early stage. Here are the planned features to be added:
* GPU auto-config _Done!_
* Separate per-GPU pools and coins (to let the CPU and each GPU mine its own coin if desired)
* Temperature and fan speed monitoring
* Per-GPU pause _Done!_
* Decent performance in Heavy/Haven
* Bittube-v2 fork support _Done!_
* APU support
* Faster and/or cached OpenCL compile

## Configuration

JCE provides an auto-config, with parameters:
* **--no-gpu --auto** to autoconfigure CPUs and no GPU
* **--no-cpu --auto** to autoconfigure GPUs and no CPU
* **--auto** to autoconfigure all capable GPUs and CPUs

If you need manual config, then all relies on the configuration file. You cannot configure CPU automatically and GPU manually, or vice-versa.\
The auto GPU config aims for safety and will probably be a decent but suboptimal configuration for your GPUs.

Here's an example of a complete config with CPUs and GPUs:

```
"cpu_threads_conf" : 
[ 
     { "cpu_architecture" : "auto", "affine_to_cpu" : 0, "use_cache" : true, "multi_hash":1 },
     { "cpu_architecture" : "auto", "affine_to_cpu" : 1, "use_cache" : true, "multi_hash":1 },
     { "cpu_architecture" : "auto", "affine_to_cpu" : 2, "use_cache" : true, "multi_hash":1 },
     { "cpu_architecture" : "auto", "affine_to_cpu" : 3, "use_cache" : true, "multi_hash":1 },
],

"gpu_threads_conf" : 
[ 
     { "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : 0, "multi_hash":464 },
     { "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : 0, "multi_hash":464 },
     { "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : 1, "multi_hash":208 },
     { "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : 1, "multi_hash":224 },
]
```

This is a real-life example from my pretty *exotic and old* rig:
* The CPU part is the exact same as in JCE CPU. I used a Core2-quad so i simply write the four normal threads.
* The GPU part is new:

*mode* : can be GPU and APU, but APU is not available yet, so keep GPU

*worksize* : exact same meaning than in other miners, that's the OpenCL parralelism level

*the greeks* : minor tuning values, in order of importance. Good values for alpha are 32, 64 or 128, and for beta 8 or 16. Other values will probably have no real impact, keep 8 or 4.

*index* : the GPU number. Run JCE with parameter --probe to get the list. It's in native OpenCL order, and GPU 0 is not always the main boot GPU.

*multi_hash* : often called intensity by other miners. Claymore parameter for it was -h. Like for CPU, that's the number of hashes computed at the same time. While CPUs go from 1 to 6, GPUs go much higher. Often the higher the faster, but not that simple. Must be a multiple of 16.

### Best known configurations

* HD7950 (Tahiti 3GB and more) Cryptonight v7
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":464 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":464 },
```
Twice the same to use the double-mem mode.


* HD7790 (Bonaire 1GB) Cryptonight v7
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":208 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":224 },
```
Two threads to use the double-mem mode, but the little 1GB VRAM doesn't allow to use 224+224 so I use 208+224.

* HD7870 or HD7850 (Pitcairn 2GB) Cryptonight v7
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":464 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":464 },
```
Twice the same to use the double-mem mode.

* RX560 (Baffin 2GB) Cryptonight v7
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 128, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":464 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 128, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta":4, "index" : ..., "multi_hash":464 },
```
Twice the same to use the double-mem mode. If a screen is plugged in the card, you may need to lower to 448 or 432

* RX570 8G Cryptonight v7
```
{ "mode": "GPU", "worksize": 4, "alpha": 128, "beta": 8, "gamma": 4, "delta": 4, "epsilon": 4, "zeta": 4, "index": ..., "multi_hash": 1008 },
{ "mode": "GPU", "worksize": 4, "alpha": 128, "beta": 8, "gamma": 4, "delta": 4, "epsilon": 4, "zeta": 4, "index": ..., "multi_hash": 1008 },
```
Twice the same to use the double-mem mode.

* RX580 4G Cryptonight v7
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash": 944},
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash": 944}, 
```
Twice the same to use the double-mem mode.

* RX580 8G Cryptonight v7

```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1696 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1696 }, 
```
Twice the same to use the double-mem mode.

* RX580 8G Cryptonight v7

```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1696 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1696 }, 
```
Twice the same to use the double-mem mode.

* RX580 8G Cryptonight-Heavy/Haven

```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":832 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":832 }, 
```
Twice the same to use the double-mem mode.

* Vega 64 Cryptonight v7
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 16, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1920 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 16, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1920 },
```
or, alternative
```
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1936 },
{ "mode" : "GPU", "worksize" : 8, "alpha" : 64, "beta" : 8, "gamma" : 4, "delta" : 4, "epsilon" : 4, "zeta" : 4, "index" : ..., "multi_hash":1936 },
```
Twice the same to use the double-mem mode.

## Troubleshooting

#### Q. My GPU is not even recognized
If it's an APU or a nVidia card, they are not supported yet.

#### Q. The OpenCL compilation fails
nVidia OpenCL driver and cards older than the HD7000 may cause such an error. They are not supported yet.

#### Q. My hashrate is zero or almost zero
Lower the parameters, and focus first on the multi_hash.

#### Q. My hashrate starts good then drops to zero or almost zero
Lower the parameters, focus first on the multi_hash, or unplug any screen (real or virtual) from the card.

#### Q. My hashrate is a lot lower than other miner X
The performance difference between JCE and other miners, may it be positive or negative, is rarely more than ~10%, and often closer to 5%. A huge difference is caused by a bad configuration. And a good configuration on miner X may be bad on JCE, and vice-versa.\
A notable exception is JCE against Claymore on 512M and 1G cards, where Claymore is an order of magnitude faster. JCE has been developped in 2018 and badly support old low-memory cards. But on 2G+ cards JCE is often faster.\
And again, keep in mind that JCE speed grows progressively from ~80% to 100% for 5min, so wait a bit before concluding JCE is slower.

#### Q. My pool reports a hashrate lower than the one displayed
First, press R to get JCE report about effective hashrate. If it's lower, that's just because of back luck, mining is a random game. If it's close to the theorical value, the loss should be about 2%, 0.9% for fees and ~1% of stale shares. If you observe a huge difference between JCE reported effective hashrate and the pool hashrate, so the pool may be cheating you.\
Nicehash, which have a specific network protocol, tend to refuse a lot more stale shares than other pools, leading to a lower effective hashrate. In doubt, please test on a normal and reliable pool: a non-free non-marketplace pool with a normal difficulty level.

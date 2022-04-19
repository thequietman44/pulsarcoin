# PulsarCoin Mining Guide

## Requirements
To get started mining PulsarCoin you will need:
 - a PC with a 64-bit x86 CPU
 - the latest [SRBMiner](https://github.com/doktor83/SRBMiner-Multi/releases) for Windows or Linux
 - A [Pulsar wallet](GettingStartedGuide.md#1-get-the-pulsar-wallet) address to receive mining rewards

## Profitability

You can see a list of hashrates reported for different CPUs here: https://github.com/pctrading/cryptodocs/wiki/PLSR#hashrates

As difficulty changes rewards may go down, but at the moment 100KH/s will earn about 25 PLSR/day.

Whether it is profitable for you to mine PLSR depends on your hardware and electricity cost. If the cost of electricity is higher than the cost to buy the same amount of PLSR/day, then you may want to [buy PLSR](GettingStartedGuide.md#2-buy-or-mine-your-first-plsr) and [stake it](GettingStartedGuide.md#3-stake-your-plsr-to-earn-more).

## Start Mining

### Choose a Mining Pool

Check the list of pools on [MiningPoolStats.stream](https://miningpoolstats.stream/pulsarcoin) and pick a pool. Some pools offer different features or have dedicated communities to offer help/advice.

### Install the Mining App

1. Download the latest [SRBMiner](https://github.com/doktor83/SRBMiner-Multi/releases) for your OS (Windows or Linux).

2. Extract the archive. You should have a folder called `SRBMiner-Multi-0-9-4` with `SRBMiner-MULTI` and a bunch of batch files (.bat) or shell scripts (.sh) called `start-mining-<coin>`. Edit the `start-mining-pulsar` file and replace the `SRBMiner-MULTI` line with the correct one for the pool you chose. See below for more details on how to [configure your miner](#configure-your-miner).

### Configure Your Miner

Many pools have a configuration generator that produces a string of text you can copy/paste into different miners. Below are two examples using Rplant and Zergpool. Replace `<PLSR_wallet>` with your Pulsar wallet receiving address, and `<Worker_Name>` with a name you would like to use to identify your miner on the pool.
```
:: Rplant
SRBMiner-MULTI.exe --algorithm curvehash --pool stratum+ssl://stratum-na.rplant.xyz:17030 --wallet <PLSR_wallet>.<Worker_Name> --disable-gpu
```
```
:: Zergpool
SRBMiner-MULTI.exe --algorithm curvehash --pool stratum+ssl://curvehash.mine.zergpool.com:13343 --wallet <PLSR wallet address>.<Worker Name> -p c=PLSR,mc=PLSR --disable-gpu
```


# PulsarCoin

A collection of files related to PulsarCoin (http://pulsarcoin.org).

Check the [Wiki](../../wiki) for step-by-step instructions.


## Script Examples
### Push Notifications
Get a push notification on your phone or desktop when your CLI wallet mints a new staking block.

<img src="images/pulsar_push.png" width="300">

### Staking Calculator
Easily calculate your total and average staking blocks/PLSR for a given number of days.

Example output:
```
./pulsar_staking_calc.sh --days 1000

Wallet age (36 days) is less than 1000, using 36 days instead...
Your wallet has minted an average of 1.38 blocks/day (124.20 PLSR/day) over the last 36 days.
Normalized to .75 blocks/day (67.50 PLSR/day) for every 10k PLSR (assuming no deposits other than staking rewards).

DAYS    BLOCKS  BLOCKS/DAY      PLSR    PLSR/DAY
36      50      1.38            4500    124.20
```
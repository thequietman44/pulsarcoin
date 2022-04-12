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

Wallet age (34 days) is less than 1000, using 34 days instead...
Your wallet has minted an average of 2.05 blocks/day (184.50 PLSR/day) over the last 34 days.

DAYS    BLOCKS  BLOCKS/DAY      PLSR    PLSR/DAY
34      70      2.05            6300    184.50
```
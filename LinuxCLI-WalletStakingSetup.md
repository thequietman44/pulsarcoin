# Linux CLI Wallet and Staking

 1. Download the latest Pulsar Linux release and extract:
 ```
 wget https://github.com/PulsarCoin/Pulsar-Coin-Cryptocurrency/releases/download/v1.1.1/pulsar-1.1.1-linux_gnu64.tar.gz
tar xvf pulsar-1.1.1-linux_gnu64.tar.gz
```
 2. Run `pulsard` in the background as a server to start syncing the blockchain:
 ```
 ./pulsard --server --daemon --banscore=10000
 ```
3. View your default wallet address, or generate a new receiving address:
```
./pulsar-cli getaccountaddress ""
```
or
```
./pulsar-cli getnewaddress "LABEL"
```
**Note**: Copy or write down this address for later, you will use it to receive PLSR from mining or exchanges.
4. Optional but recommended, encrypt your wallet with a passphrase:
```
./pulsar-cli encryptwallet "YOUR PASSPHRASE HERE"
```
5. After encrypting your wallet `pulsard` is stopped, simply re-run the command from step 2 to restart it:
```
./pulsard --daemon --server --banscore=10000
```
6. You can unlock your wallet for staking only with the following command:
```
./pulsar-cli walletpassphrase "YOUR PASSPHRASE HERE" 960000 true
```
7. You can view the status of your wallet with this command:
```
./pulsar-cli getwalletinfo
```
```
{
  "walletname": "wallet.dat",
  "walletversion": 159900,
  "balance": 0.000000,
  "unconfirmed_balance": 0.000000,
  "immature_balance": 0.000000,
  "txcount": 0,
  "keypoololdest": 1646928903,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 1647889297,
  "unlocked_staking_only": true,
  "hdmasterkeyid": "60f90fca29c58fb1b43d4adfb8b68d62e552948a"
}
```
8. There are many other commands available, to see them all run this:
```
./pulsar-cli help
```

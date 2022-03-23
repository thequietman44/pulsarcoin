## Linux CLI Wallet - Push Notifications for Staking

### Requirements
You will need a free Pushover account (https://pushover.net/) for the push notifications. Any other push service API could also work, but for the purposes of this guide we'll use Pushover.
You will need your "User Key" and an "API Token/Key" from your Pushover account. You will also need at least 1 device registered with Pushover to receive push notifications, details can be found in Pushover's documentation.

### Pushover Script Setup 
 
1. Install `wget` if it's not already installed:
```
sudo apt install wget -y
```
2. Create the `pushover` script in `/usr/local/bin/`:
 ```
sudo nano /usr/local/bin/pushover
```
3. Paste the following into the new file, inserting your own values for `APP_TOKEN` and `USER_TOKEN`:
 ```
#!/bin/bash

if [ $# -eq 0 ]; then
        echo "Usage: pushover <message> [title]"
        exit
fi

MESSAGE=$1
TITLE=$2

if [ $# -lt 2 ]; then
        TITLE="`whoami`@${HOSTNAME}"
fi

APP_TOKEN="xxxxxxxxxx"
USER_TOKEN="xxxxxxxxxx"

wget https://api.pushover.net/1/messages.json --post-data="token=$APP_TOKEN&user=$USER_TOKEN&message=$MESSAGE&title=$TITLE" -qO- > /dev/null 2>&1 &
 ```
4. Hit `Ctrl+O` and `Enter` to save your changes, then `Ctrl+X` to close the file.
5. Make the script executable:
```
sudo chmod +x /usr/local/bin/pushover
```
6. Test push notifications:
```
pushover "This is a test message"
```
7. You should receive a push notification within a few seconds. If not, make sure you pasted the correct User and API key. 
**Note:** You can now use the `pushover` command to send push notifications from any script or terminal on this PC.


### Staking Reward Script Setup

1. Install one dependency `jq`:
```
sudo apt install jq -y
```
2. Create a new directory to store the script and files:
```
mkdir ~/pulsar-scripts && cd ~/pulsar-scripts
```
3. Create the script file:
```
nano pulsar_wallet_push.sh
```
4. Paste the following into the new file, changing the two path variables if you chose different names:
 ```
#!/bin/bash

# Paths, change if you chose different names/locations
PULSARDIR=~
SCRIPTDIR=~/pulsar-scripts

# Files
BLOCKHASHFILE="$SCRIPTDIR/blockhashes.txt"

touch $BLOCKHASHFILE

# Variables
CURRENTTIME=$(date +%s)
UNLOCKEDTIME=`$PULSARDIR/pulsar-cli getwalletinfo | jq '.unlocked_until'`
TIMEDIFF=$(expr $UNLOCKEDTIME - $CURRENTTIME)
TIMEDIFFMIN=$(expr $TIMEDIFF / 60)
TIMEDIFFHOUR=$(expr $TIMEDIFF / 60 / 60)
TIMEDIFFDAY=$(expr $TIMEDIFF / 60 / 60 / 24)
BLOCKHASHES=`$PULSARDIR/pulsar-cli listtransactions | jq '.[] | select(.category=="stake-mint") | .blockhash'`

# Warn if wallet is not unlocked for staking
if [ $TIMEDIFF -le 0 ]; then
    pushover "WARNING: Pulsar wallet is locked, not staking!"
    echo "WARNING: Pulsar wallet is locked, not staking!"

# If wallet unlocked time remaining is less than 12 hours, send push
elif [ $TIMEDIFFHOUR -lt 12 ]; then
    pushover "WARNING: Pulsar wallet locking in $TIMEDIFFHOUR hours!"
    echo  "WARNING: Pulsar wallet locking in $TIMEDIFFHOUR hours!"

else
    echo "Wallet unlocked for $TIMEDIFFDAY more days, until $(date --date=@$UNLOCKEDTIME)"
fi


# If any of the block hashes were not previously logged, send a push for each new block
for i in $BLOCKHASHES; do
    if [ $(grep -c $i $BLOCKHASHFILE) -eq 0 ]; then
        echo "PoS block found with hash $i"
        pushover "PoS block found with hash $i"
    fi
done

echo $BLOCKHASHES > $BLOCKHASHFILE
 ```
5. Hit `Ctrl+O` and `Enter` to save your changes, then `Ctrl+X` to close the file.
6. Make the script executable:
```
chmod +x pulsar_wallet_push.sh
```
7. Run the script once manually to make sure there are no errors:
```
./pulsar_wallet_push.sh
```
8. If you already had staking rewards you should see push notifications for each of them now. Otherwise you should only see the message with the wallet unlock timeout info.

### Cron Job Setup

1. Edit your crontab file:
```
crontab -e
```
**Note:** If this is the first time you've edited your crontab you will be prompted to choose an editor. In this example I will use `nano`.

2. Scroll to the bottom of the crontab file and paste one of the following lines according to how often you would like to check for PoS blocks.

Run every 15 minutes:
```
*/15 * * * * ~/pulsar-scripts/pulsar_wallet_push.sh
```
Run every 1 hour:
```
* */1 * * * ~/pulsar-scripts/pulsar_wallet_push.sh
```
3. Hit `Ctrl+O` and `Enter` to save your changes, then `Ctrl+X` to close the file.

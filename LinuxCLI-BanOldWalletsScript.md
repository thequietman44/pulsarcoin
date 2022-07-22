# Linux CLI - Ban Old Wallets Script

## Ban Script Setup
1. Install two dependencies `jq` and `bc`:
```
sudo apt install jq bc -y
```
2. Create a new directory to store the script:
```
mkdir ~/pulsar-scripts && cd ~/pulsar-scripts
```
3. Create the script file:
```
nano ban_old_wallets.sh
```
4. Paste the following into the new file, changing the `PULSARDIR` path variable if you installed the wallet elsewhere:
```
#!/bin/bash

# Paths, change if you chose different names/locations
PULSARDIR=~

# Get a list of peers, return the IP of any peers whose subver string starts with "/Satoshi"
SAT_IPs=`$PULSARDIR/pulsar-cli getpeerinfo | jq -r '.[] | select(.subver | startswith("/Satoshi")) | .addr'`

# Also get the IP of peers running older wallet versions
OLD_IPs=`$PULSARDIR/pulsar-cli getpeerinfo | jq -r '.[] | select(.subver | (startswith("/Pulsar:1.1.3")) | not) | .addr'`

# Function to ban IPv4 and IPv6 addresses
ban_ips () {
    for IP in $1; do

        if [[ $IP =~ "[" ]]; then
            # If IP contains brackets it's IPv6, return address inside brackets
            PARSED_IP=`echo $IP | awk -F]: '{ print $1 }' | awk -F[ '{ print $2 }'`
        else
            # If IP doesn't contain brackets it's IPv4, return address before the :
            PARSED_IP=`echo $IP | awk -F: '{ print $1 }'`
        fi

        echo "$PULSARDIR/pulsar-cli setban \"$PARSED_IP\" \"add\" 604800"
        #$PULSARDIR/pulsar-cli setban "$PARSED_IP" "add" 604800
    done
}


# If any IPs were returned, ban them for 1 week
if [ ! -z "$SAT_IPs" ]; then
    echo "Satoshi nodes found:"
    echo $SAT_IPs
    echo ""

    ban_ips "$SAT_IPs"

else
    echo "No Satoshi nodes found."
fi


if [ ! -z "$OLD_IPs" ]; then
    echo "Old nodes found:"
    echo $OLD_IPs
    echo ""

    ban_ips "$OLD_IPs"

else
    echo "No old nodes found."
fi
```
5. Hit `Ctrl+O` and `Enter` to save your changes, then `Ctrl+X` to close the file.
6. Make the script executable:
```
chmod +x ban_old_wallets.sh
```
7. Run the script manually to make sure it's working:
```
./ban_old_wallets.sh
```

## Cron Job Setup

1. Edit your crontab file:
```
crontab -e
```
**Note:** If this is the first time you've edited your crontab you will be prompted to choose an editor. In this example I will use `nano`.

2. Scroll to the bottom of the crontab file and paste one of the following lines according to how often you would like to check for PoS blocks.

Run every 15 minutes:
```
*/15 * * * * ~/pulsar-scripts/ban_old_wallets.sh
```
Run every 1 hour:
```
* */1 * * * ~/pulsar-scripts/ban_old_wallets.sh
```
3. Hit `Ctrl+O` and `Enter` to save your changes, then `Ctrl+X` to close the file.
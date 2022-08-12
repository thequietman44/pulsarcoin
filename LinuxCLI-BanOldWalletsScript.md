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

######### User variables, edit as needed #########
# Paths, change if you chose different names/locations
PULSARDIR=~

# Whitelist of IPs or addresses that should never be banned (separated by spaces)
WHITELIST="pulsarcoin.org"

# Current wallet version string, ban any peers not running this version
WALLET_SUBVER="/Pulsar:1.1.3"

# Ban period in days
BANDAYS=7

# Experimental, ping latency in ms
PING_MS=200 # Peers with minimum ping latency greater than this many milliseconds will be banned

######### End user variables ##########

# Convert days to seconds
BANLENGTH=`echo "scale=0; $BANDAYS * 86400" | bc`

# Get a list of peers, return the IP of any peers whose subver string starts with "/Satoshi"
SAT_IPs=`$PULSARDIR/pulsar-cli getpeerinfo | jq -r '.[] | select(.subver | startswith("/Satoshi")) | .addr'`

# Also get the IP of peers running older wallet versions
OLD_IPs=`$PULSARDIR/pulsar-cli getpeerinfo | jq --arg subverstring "$WALLET_SUBVER" -r '.[] | select(.subver | (startswith($subverstring)) | not) | .addr'`

# Experimental, get list of IPs with ping latency above specified threshold in ms
PING=`echo "scale=4; $PING_MS / 1000" | bc`
SLOW_IPs=`$PULSARDIR/pulsar-cli getpeerinfo | jq --arg ping "$PING" -r '.[] | select(.minping>=($ping|tonumber)) | .addr'`

# Function to ban IPv4 and IPv6 addresses
ban_ips () {
    for IP in $1; do

        SKIP_IP=0

        if [[ $IP =~ "[" ]]; then
            # If IP contains brackets it's IPv6, return address inside brackets
            PARSED_IP=`echo $IP | awk -F]: '{ print $1 }' | awk -F[ '{ print $2 }'`
        else
            # If IP doesn't contain brackets it's IPv4, return address before the :
            PARSED_IP=`echo $IP | awk -F: '{ print $1 }'`
        fi

        for i in $WHITELIST; do
            if [[ $PARSED_IP == *"$i"* ]]; then
                echo "I found a whitelisted IP $PARSED_IP, skipping..."
                SKIP_IP=1
            fi
        done

        # If SKIP_IP hasn't been set to 1 (true) proceed with banning the IP
        if [ $SKIP_IP = 0 ]; then
            echo "$PULSARDIR/pulsar-cli setban \"$PARSED_IP\" \"add\" $BANLENGTH"
            $PULSARDIR/pulsar-cli setban "$PARSED_IP" "add" $BANLENGTH
        fi

    done
}


# If any IPs were returned, ban them
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

if [ ! -z "$SLOW_IPs" ]; then
    echo "Slow nodes found:"
    echo $SLOW_IPs
    echo ""
    #ban_ips "$SLOW_IPs"
else
    echo "No slow nodes found."
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
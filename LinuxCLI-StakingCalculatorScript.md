### Staking Calculator Script Setup

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
nano pulsar_staking_calc.sh
```
4. Paste the following into the new file, changing the `PULSARDIR` path variable if you installed the wallet elsewhere:
```
#!/bin/bash

# Paths, change if you chose different names/locations
PULSARDIR=~

# Make sure bc is installed for floating point math
if [ ! `which bc` ]; then
        echo "Please install the 'bc' command by running:"
        echo "sudo apt install bc"
        exit 1
fi

echo ""

# If no --days argument was provided, use the default 7-day average
if [[ $# -eq 0 ]] ; then
        echo "Using default 7-day average"
        DAYS=7
else
  # If an argument was provided, sanity check and parse
  POSITIONAL_ARGS=()

  while [[ $# -gt 0 ]]; do
    case $1 in
      --days)
        DAYS="$2"
        shift # past argument
        shift # past value
        ;;
      -*|--*)
        echo "Unknown option $1"
        exit 1
        ;;
      *)
        POSITIONAL_ARGS+=("$1") # save positional arg
        shift # past argument
        ;;
    esac
  done
fi

STARTTIME=`expr $(date +%s) - $(expr $DAYS \* 24 \* 60 \* 60)`
OLDESTTX=`$PULSARDIR/pulsar-cli listtransactions "*" 100000 | jq '.[] | (.time | tonumber)' | head -1`
WALLETAGE=`echo "scale=0; ($(date +%s) - $OLDESTTX) / 60 / 60 / 24" | bc`

if [ "$STARTTIME" -lt "$OLDESTTX" ]; then
        echo "Wallet age ($WALLETAGE days) is less than $DAYS, using $WALLETAGE days instead..."
        STARTTIME=$OLDESTTX
        DAYS=$WALLETAGE
fi

BLOCKS=`$PULSARDIR/pulsar-cli listtransactions "*" 100000 | jq '.[] | select(.category=="stake-mint") | select((.time | tonumber)>='$STARTTIME')' | grep -c stake-mint`
AVGBLOCKS=`echo "scale=2; $BLOCKS / $DAYS" | bc`
PLSR=`expr $BLOCKS \* 90`
AVGPLSR=`echo "scale=4; $AVGBLOCKS * 90" | bc`

echo "Your wallet has minted an average of $AVGBLOCKS blocks/day ($AVGPLSR PLSR/day) over the last $DAYS days."
echo ""
echo "DAYS      BLOCKS  BLOCKS/DAY      PLSR    PLSR/DAY"
echo "$DAYS     $BLOCKS $AVGBLOCKS              $PLSR   $AVGPLSR"

```
5. Hit `Ctrl+O` and `Enter` to save your changes, then `Ctrl+X` to close the file.
6. Make the script executable:
```
chmod +x pulsar_staking_calc.sh
```
7. Run the script with the `--days` argument to specify how many days to average, or run without arguments to use the default of 7 days:
```
./pulsar_staking_calc.sh --days 14
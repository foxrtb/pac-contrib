#simple scripts that compares the local chain with the explorer, and if wrong posts a message on discord
#
```
tee /usr/local/bin/block.sh  &>/dev/null <<'EOF'

#Discord webhooks url
url='https://discordapp.com/api/webhooks/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

BLOCK=$(/usr/local/bin/paccoin-cli getblockcount)
BLOCKHASH=$(/usr/local/bin/paccoin-cli getblockhash $BLOCK)
EXPLORER=$(/usr/bin/curl -sS http://explorer.foxrtb.com/api/block-index/$BLOCK| jq -r .[])
DATE=$(date "+%Y-%m-%dT%T")
HOST=`hostname`


if [ "$BLOCKHASH" != "$EXPLORER" ]; then
  /usr/bin/curl -H "Content-Type: application/json" -X POST -d "{\"content\": \"$BLOCK "local" $BLOCKHASH "explorer" $EXPLORER  $DATE   $HOST\"}" $url
fi
EOF


crontab -l | { cat; echo "0 * * * * /usr/local/bin/block.sh >/dev/null 2>&1"; } | crontab -
```

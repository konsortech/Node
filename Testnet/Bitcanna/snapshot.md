## Daily Snapshot (Every 6 Hours)
```
sudo systemctl stop bcnad
cp $HOME/.bcna/data/priv_validator_state.json $HOME/.bcna/priv_validator_state.json.backup
rm -rf $HOME/.bcna/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/bitcanna-testnet/ | egrep -o ">bitcanna-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/bitcanna-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.bcna
mv $HOME/.bcna/priv_validator_state.json.backup $HOME/.bcna/data/priv_validator_state.json

sudo systemctl restart bcnad && journalctl -u bcnad -f --no-hostname -o cat
```

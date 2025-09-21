## Daily Snapshot
```
sudo systemctl stop iris
cp $HOME/.iris/data/priv_validator_state.json $HOME/.iris/priv_validator_state.json.backup
rm -rf $HOME/.iris/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/mainnet/irisnet/ | egrep -o ">iris-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/mainnet/irisnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.iris
mv $HOME/.iris/priv_validator_state.json.backup $HOME/.iris/data/priv_validator_state.json

sudo systemctl restart iris && journalctl -u iris -f --no-hostname -o cat
```

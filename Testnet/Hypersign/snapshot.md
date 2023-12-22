## Snapshot
```
sudo systemctl stop hid-noded
cp $HOME/.hid-node/data/priv_validator_state.json $HOME/.hid-node/priv_validator_state.json.backup
rm -rf $HOME/.hid-node/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/hypersign/ | egrep -o ">hypersign-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/hypersign/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.hid-node
mv $HOME/.hid-node/priv_validator_state.json.backup $HOME/.hid-node/data/priv_validator_state.json

sudo systemctl restart hid-noded && journalctl -u hid-noded -f --no-hostname -o cat
```

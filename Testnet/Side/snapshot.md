## Snapshots
```
sudo systemctl stop sided
cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
rm -rf $HOME/.side/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/side-testnet/ | egrep -o ">side-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/side-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.side
mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json

sudo systemctl restart sided && journalctl -u sided -f --no-hostname -o cat
```

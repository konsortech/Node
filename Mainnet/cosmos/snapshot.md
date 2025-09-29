## Snapshot
```
sudo systemctl stop gaiad
cp $HOME/.gaia/data/priv_validator_state.json $HOME/.gaia/priv_validator_state.json.backup
rm -rf $HOME/.gaia/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/mainnet/cosmos/ | egrep -o ">cosmos-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/mainnet/cosmos/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.gaia
mv $HOME/.gaia/priv_validator_state.json.backup $HOME/.gaia/data/priv_validator_state.json

sudo systemctl restart gaiad && journalctl -u gaiad -f --no-hostname -o cat
```

## Snapshot
```
sudo systemctl stop artelad
cp $HOME/.artelad/data/priv_validator_state.json $HOME/.artelad/priv_validator_state.json.backup
rm -rf $HOME/.artelad/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/artela-testnet/ | egrep -o ">artela-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/artela-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.artelad
mv $HOME/.artelad/priv_validator_state.json.backup $HOME/.artelad/data/priv_validator_state.json

sudo systemctl restart artelad && journalctl -u artelad -f --no-hostname -o cat
```

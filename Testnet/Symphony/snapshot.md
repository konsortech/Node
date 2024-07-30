## Snapshot
```
sudo systemctl stop .symphonyd
cp $HOME/.symphonyd/data/priv_validator_state.json $HOME/.symphonyd/priv_validator_state.json.backup
rm -rf $HOME/.symphonyd/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/symphony/ | egrep -o ">symphony-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap2.konsortech.xyz/symphony/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.symphonyd
mv $HOME/.symphonyd/priv_validator_state.json.backup $HOME/.symphonyd/data/priv_validator_state.json

sudo systemctl restart .symphonyd && journalctl -u .symphonyd -f --no-hostname -o cat
```

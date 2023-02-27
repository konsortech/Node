## Snapshot

```
sudo systemctl stop curiumd
cp $HOME/.curium/data/priv_validator_state.json $HOME/.curium/priv_validator_state.json.backup
rm -rf $HOME/.curium/data

SNAP_NAME=$(curl -s https://snapshot1.konsortech.xyz/bluzelle/ | egrep -o ">bluzelle-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot1.konsortech.xyz/bluzelle/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.curium
mv $HOME/.curium/priv_validator_state.json.backup $HOME/.curium/data/priv_validator_state.json

sudo systemctl restart curiumd && journalctl -u curiumd -f -o cat
```

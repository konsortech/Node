## Snapshot
```
sudo systemctl stop uniond
cp $HOME/.union/data/priv_validator_state.json $HOME/.union/priv_validator_state.json.backup
rm -rf $HOME/.union/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/union/ | egrep -o ">union-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/union/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.union
mv $HOME/.union/priv_validator_state.json.backup $HOME/.union/data/priv_validator_state.json

sudo systemctl restart uniond && journalctl -u uniond -f --no-hostname -o cat
```

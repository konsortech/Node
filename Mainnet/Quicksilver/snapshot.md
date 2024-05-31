## Snapshot
```
sudo systemctl stop quicksilverd
cp $HOME/.quicksilverd/data/priv_validator_state.json $HOME/.quicksilverd/priv_validator_state.json.backup
rm -rf $HOME/.quicksilverd/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/quicksilver/ | egrep -o ">quicksilver-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/quicksilver/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.quicksilverd
mv $HOME/.quicksilverd/priv_validator_state.json.backup $HOME/.quicksilverd/data/priv_validator_state.json

sudo systemctl restart quicksilverd && journalctl -u quicksilverd -f --no-hostname -o cat
```

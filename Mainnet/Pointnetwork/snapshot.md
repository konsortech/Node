## Snapshot
```
sudo systemctl stop pointd
cp $HOME/.pointd/data/priv_validator_state.json $HOME/.pointd/priv_validator_state.json.backup
rm -rf $HOME/.pointd/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/point/ | egrep -o ">point-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/point/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.pointd
mv $HOME/.pointd/priv_validator_state.json.backup $HOME/.pointd/data/priv_validator_state.json

sudo systemctl restart pointd && journalctl -u pointd -f --no-hostname -o cat
```

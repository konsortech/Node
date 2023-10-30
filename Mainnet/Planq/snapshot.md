## Daily Snapshot
```
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/planq/ | egrep -o ">planq-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/planq/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json

sudo systemctl restart planqd && journalctl -u planqd -f --no-hostname -o cat
```

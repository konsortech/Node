## Snapshot
```
sudo systemctl stop oraid
cp $HOME/.oraid/data/priv_validator_state.json $HOME/.oraid/priv_validator_state.json.backup
rm -rf $HOME/.oraid/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/orai/ | egrep -o ">orai-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/orai/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.oraid
mv $HOME/.oraid/priv_validator_state.json.backup $HOME/.oraid/data/priv_validator_state.json

sudo systemctl restart oraid && journalctl -u oraid -f --no-hostname -o cat
```

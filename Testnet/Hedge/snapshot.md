## Snapshot
```
sudo systemctl stop hedged
cp $HOME/.hedge/data/priv_validator_state.json $HOME/.hedge/priv_validator_state.json.backup
rm -rf $HOME/.hedge/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/hedge/ | egrep -o ">hedge-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/hedge/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.hedge
mv $HOME/.hedge/priv_validator_state.json.backup $HOME/.hedge/data/priv_validator_state.json

sudo systemctl restart hedged && journalctl -u hedged -f --no-hostname -o cat
```

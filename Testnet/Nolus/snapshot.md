## Snapshot
```
sudo apt install lz4
sudo systemctl stop nolusd
cp $HOME/.nolus/data/priv_validator_state.json $HOME/.nolus/priv_validator_state.json.backup
rm -rf $HOME/.nolus/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/nolus/ | egrep -o ">nolus-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/nolus/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.nolus
mv $HOME/.nolus/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json

sudo systemctl restart nolusd && journalctl -u nolusd -f --no-hostname -o cat
```

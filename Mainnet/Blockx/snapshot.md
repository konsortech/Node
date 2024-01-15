## Snapshot
```
sudo systemctl stop blockxd
cp $HOME/.blockxd/data/priv_validator_state.json $HOME/.blockxd/priv_validator_state.json.backup
rm -rf $HOME/.blockxd/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/blockx/ | egrep -o ">blockx-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/blockx/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.blockxd
mv $HOME/.blockxd/priv_validator_state.json.backup $HOME/.blockxd/data/priv_validator_state.json

sudo systemctl restart blockxd && journalctl -u blockxd -f --no-hostname -o cat
```

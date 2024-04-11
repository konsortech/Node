## Snapshot
```
sudo systemctl stop crossfid
cp $HOME/.mineplex-chain/data/priv_validator_state.json $HOME/.mineplex-chain/priv_validator_state.json.backup
rm -rf $HOME/.mineplex-chain/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/crossfi/ | egrep -o ">crossfi-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/crossfi/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain
mv $HOME/.mineplex-chain/priv_validator_state.json.backup $HOME/.mineplex-chain/data/priv_validator_state.json

sudo systemctl restart crossfid && journalctl -u crossfid -f --no-hostname -o cat
```

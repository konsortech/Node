## Snapshots
```
sudo systemctl stop acred
cp $HOME/.acred/data/priv_validator_state.json $HOME/.acred/priv_validator_state.json.backup
rm -rf $HOME/.acred/data

SNAP_NAME=$(curl -s https://snapshot1.konsortech.xyz/acre/ | egrep -o ">acre-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/acre/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.acred
mv $HOME/.acred/priv_validator_state.json.backup $HOME/.acred/data/priv_validator_state.json

sudo systemctl restart acred && journalctl -u acred -f --no-hostname -o cat
```

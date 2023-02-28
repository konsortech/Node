## Daily Snapshot
```
sudo systemctl stop lumenxd
cp $HOME/.lumenx/data/priv_validator_state.json $HOME/.lumenx/priv_validator_state.json.backup
rm -rf $HOME/.lumenx/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/lumenx/ | egrep -o ">lumenx-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot1.konsortech.xyz/lumenx/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.lumenx
mv $HOME/.lumenx/priv_validator_state.json.backup $HOME/.lumenx/data/priv_validator_state.json

sudo systemctl restart lumenxd && journalctl -u lumenxd -f --no-hostname -o cat
```

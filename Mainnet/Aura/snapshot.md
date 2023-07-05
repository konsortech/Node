## Daily Snapshot (Every 12 Hours)
```
sudo systemctl stop aurad
cp $HOME/.aura/data/priv_validator_state.json $HOME/.aura/priv_validator_state.json.backup
rm -rf $HOME/.aura/data

SNAP_NAME=$(curl -s https://snapshot2.konsortech.xyz/aura/ | egrep -o ">aura-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot2.konsortech.xyz/aura/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.aura
mv $HOME/.aura/priv_validator_state.json.backup $HOME/.aura/data/priv_validator_state.json

sudo systemctl restart aurad && journalctl -u aurad -f --no-hostname -o cat
```

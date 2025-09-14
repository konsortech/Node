## Daily Snapshot (Every 6 Hours)
```
sudo systemctl stop gitopiad
cp $HOME/.gitopia/data/priv_validator_state.json $HOME/.gitopia/priv_validator_state.json.backup
rm -rf $HOME/.gitopia/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/gitopia/ | egrep -o ">gitopia-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/gitopia/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.gitopia
mv $HOME/.gitopia/priv_validator_state.json.backup $HOME/.gitopia/data/priv_validator_state.json

sudo systemctl restart gitopiad && journalctl -u gitopiad -f --no-hostname -o cat
```

## Snapshot
```
sudo systemctl stop exrpd
cp $HOME/.exrpd/data/priv_validator_state.json $HOME/.exrpd/priv_validator_state.json.backup
rm -rf $HOME/.exrpd/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/xrpl/ | egrep -o ">xrpl-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/xrpl/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.exrpd
mv $HOME/.exrpd/priv_validator_state.json.backup $HOME/.exrpd/data/priv_validator_state.json

sudo systemctl restart exrpd && journalctl -u exrpd -f --no-hostname -o cat
```

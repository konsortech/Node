## Snapshot
```
sudo systemctl stop sedad
cp $HOME/.sedad/data/priv_validator_state.json $HOME/.sedad/priv_validator_state.json.backup
rm -rf $HOME/.sedad/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/seda/ | egrep -o ">seda-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap2.konsortech.xyz/seda/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.sedad
mv $HOME/.sedad/priv_validator_state.json.backup $HOME/.sedad/data/priv_validator_state.json

sudo systemctl restart sedad && journalctl -u sedad -f --no-hostname -o cat
```

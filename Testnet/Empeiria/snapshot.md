## Snapshot
```
sudo systemctl stop emped
cp $HOME/.empe-chain/data/priv_validator_state.json $HOME/.empe-chain/priv_validator_state.json.backup
rm -rf $HOME/.empe-chain/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/empeiria/ | egrep -o ">empeiria-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap2.konsortech.xyz/empeiria/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.empe-chain
mv $HOME/.empe-chain/priv_validator_state.json.backup $HOME/.empe-chain/data/priv_validator_state.json

sudo systemctl restart emped && journalctl -u emped -f --no-hostname -o cat
```

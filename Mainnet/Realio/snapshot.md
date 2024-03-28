## Daily Snapshot (Every 12 Hours)
```
sudo systemctl stop realio-networkd
cp $HOME/.realio-network/data/priv_validator_state.json $HOME/.realio-network/priv_validator_state.json.backup
rm -rf $HOME/.realio-network/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/realio/ | egrep -o ">realio-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/realio/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.realio-network
mv $HOME/.realio-network/priv_validator_state.json.backup $HOME/.realio-network/data/priv_validator_state.json

sudo systemctl restart realio-networkd && journalctl -u realio-networkd -f --no-hostname -o cat
```

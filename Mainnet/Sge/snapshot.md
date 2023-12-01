## Snapshot
```
sudo systemctl stop sged
cp $HOME/.sge/data/priv_validator_state.json $HOME/.sge/priv_validator_state.json.backup
rm -rf $HOME/.sge/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/sge/ | egrep -o ">sge-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/sge/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.sge
mv $HOME/.sge/priv_validator_state.json.backup $HOME/.sge/data/priv_validator_state.json

sudo systemctl restart sged && journalctl -u sged -f --no-hostname -o cat
```

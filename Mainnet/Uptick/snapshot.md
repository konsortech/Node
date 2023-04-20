
## Daily Snapshot (Every 12 Hours)
```
sudo systemctl stop uptickd
cp $HOME/.uptickd/data/priv_validator_state.json $HOME/.uptickd/priv_validator_state.json.backup
rm -rf $HOME/.uptickd/data

SNAP_NAME=$(curl -s https://snapshot2.konsortech.xyz/uptickd/ | egrep -o ">uptickd-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/uptickd/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.uptickd
mv $HOME/.uptickd/priv_validator_state.json.backup $HOME/.uptickd/data/priv_validator_state.json

sudo systemctl restart uptickd && journalctl -u uptickd -f --no-hostname -o cat
```

## Snapshot
```
sudo systemctl stop junctiond
cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
rm -rf $HOME/.junction/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/airchains/ | egrep -o ">airchains-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/airchains/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.junction
mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json

sudo systemctl restart junctiond && journalctl -u junctiond -f --no-hostname -o cat
```

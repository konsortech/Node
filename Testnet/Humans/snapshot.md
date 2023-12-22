## Snapshot
```
sudo systemctl stop humansd
cp $HOME/.humansd/data/priv_validator_state.json $HOME/.humansd/priv_validator_state.json.backup
rm -rf $HOME/.humansd/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/humans-testnet/ | egrep -o ">humans-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/humans-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.humansd
mv $HOME/.humansd/priv_validator_state.json.backup $HOME/.humansd/data/priv_validator_state.json

sudo systemctl restart humansd && journalctl -u humansd -f --no-hostname -o cat
```

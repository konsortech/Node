## Snapshot
```
sudo systemctl stop humansd
cp $HOME/.humansd/data/priv_validator_state.json $HOME/.humansd/priv_validator_state.json.backup
rm -rf $HOME/.humansd/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/humans/ | egrep -o ">haqq-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/humans/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.humansd
mv $HOME/.humansd/priv_validator_state.json.backup $HOME/.humansd/data/priv_validator_state.json

sudo systemctl restart humansd && journalctl -u humansd -f --no-hostname -o cat
```

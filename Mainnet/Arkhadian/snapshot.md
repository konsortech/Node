## Snapshot
```
sudo systemctl stop arkh
cp $HOME/.arkh/data/priv_validator_state.json $HOME/.arkh/priv_validator_state.json.backup
rm -rf $HOME/.arkh/data

SNAP_NAME=$(curl -s https://snapshot1.konsortech.xyz/arkh/ | egrep -o ">arkh-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot1.konsortech.xyz/arkh/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.arkh
mv $HOME/.arkh/priv_validator_state.json.backup $HOME/.arkh/data/priv_validator_state.json

sudo systemctl restart arkh && journalctl -u arkh -f --no-hostname -o cat
```

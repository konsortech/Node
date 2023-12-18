## Snapshot
```
sudo systemctl stop pryzmd
cp $HOME/.pryzm/data/priv_validator_state.json $HOME/.pryzm/priv_validator_state.json.backup
rm -rf $HOME/.pryzm/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/pryzm-testnet/ | egrep -o ">pryzm-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/pryzm-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.pryzm
mv $HOME/.pryzm/priv_validator_state.json.backup $HOME/.pryzm/data/priv_validator_state.json

sudo systemctl restart pryzmd && journalctl -u pryzmd -f --no-hostname -o cat
```

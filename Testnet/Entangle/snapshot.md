## Snapshot
```
sudo systemctl stop entangled
cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup
rm -rf $HOME/.entangled/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/entangle/ | egrep -o ">entangle-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/entangle/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.entangled
mv $HOME/.entangled/priv_validator_state.json.backup $HOME/.entangled/data/priv_validator_state.json

sudo systemctl restart entangled && journalctl -u entangled -f --no-hostname -o cat
```

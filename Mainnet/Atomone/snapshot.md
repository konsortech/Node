## Snapshot
```
sudo systemctl stop atomoned
cp $HOME/.atomone/data/priv_validator_state.json $HOME/.atomone/priv_validator_state.json.backup
rm -rf $HOME/.atomone/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/atomone/ | egrep -o ">atomone-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/atomone/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.atomone
mv $HOME/.atomone/priv_validator_state.json.backup $HOME/.atomone/data/priv_validator_state.json

sudo systemctl restart atomoned && journalctl -u atomoned -f --no-hostname -o cat
```

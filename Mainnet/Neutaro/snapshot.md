## Snapshot
```
sudo systemctl stop neutarod
cp $HOME/.Neutaro/data/priv_validator_state.json $HOME/.Neutaro/priv_validator_state.json.backup
rm -rf $HOME/.Neutaro/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/neutaro/ | egrep -o ">neutaro-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/neutaro/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.Neutaro
mv $HOME/.Neutaro/priv_validator_state.json.backup $HOME/.Neutaro/data/priv_validator_state.json

sudo systemctl restart neutarod && journalctl -u neutarod -f --no-hostname -o cat
```

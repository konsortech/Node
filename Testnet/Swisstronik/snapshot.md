## Snapshot
```
sudo systemctl stop swisstronikd
cp $HOME/.swisstronik/data/priv_validator_state.json $HOME/.swisstronik/priv_validator_state.json.backup
rm -rf $HOME/.swisstronik/data

SNAP_NAME=$(curl -s https://snap.konsortech.xyz/swisstronik/ | egrep -o ">swisstronik-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap.konsortech.xyz/swisstronik/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.swisstronik
mv $HOME/.swisstronik/priv_validator_state.json.backup $HOME/.swisstronik/data/priv_validator_state.json

sudo systemctl restart swisstronikd && journalctl -u swisstronikd -f --no-hostname -o cat
```

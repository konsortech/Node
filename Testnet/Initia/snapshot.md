## Snapshot
```
sudo systemctl stop initiad
cp $HOME/.initia/data/priv_validator_state.json $HOME/.initia/priv_validator_state.json.backup
rm -rf $HOME/.initia/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/initia/ | egrep -o ">initia-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/initia/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.initia
mv $HOME/.initia/priv_validator_state.json.backup $HOME/.initia/data/priv_validator_state.json

sudo systemctl restart initiad && journalctl -u initiad -f --no-hostname -o cat
```

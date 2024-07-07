## Snapshot
```
sudo systemctl stop selfchaind
cp $HOME/.selfchain/data/priv_validator_state.json $HOME/.selfchain/priv_validator_state.json.backup
rm -rf $HOME/.selfchain/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/selfchain/ | egrep -o ">selfchain-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap2.konsortech.xyz/selfchain/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.selfchain
mv $HOME/.selfchain/priv_validator_state.json.backup $HOME/.selfchain/data/priv_validator_state.json

sudo systemctl restart selfchaind && journalctl -u selfchaind -f --no-hostname -o cat
```

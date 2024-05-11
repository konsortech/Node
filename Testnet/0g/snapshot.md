## Snapshot
```
sudo systemctl stop 0gchaind
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
rm -rf $HOME/.0gchain/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/0gchain/ | egrep -o ">0gchain-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/0gchain/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.0gchain
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json

sudo systemctl restart 0gchaind && journalctl -u 0gchaind -f --no-hostname -o cat
```

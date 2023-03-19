## Snapshots
```
sudo systemctl stop oraid
cp $HOME/.oraid/data/priv_validator_state.json $HOME/.oraid/priv_validator_state.json.backup
rm -rf $HOME/.oraid/data


curl https://orai-stockholm.s3.eu-north-1.amazonaws.com/mainnet_statesync_data_9149946_wasm_at_9149946_v0410.tar.gz | lz4 -dc - | tar -xf - -C $HOME/.oraid
mv $HOME/.oraid/priv_validator_state.json.backup $HOME/.oraid/data/priv_validator_state.json

sudo systemctl restart oraid && journalctl -u oraid -f --no-hostname -o cat
```

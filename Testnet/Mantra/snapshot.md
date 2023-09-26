## Snapshot
```
sudo apt install lz4
sudo systemctl stop mantrachaind
cp $HOME/.mantrachain/data/priv_validator_state.json $HOME/.mantrachain/priv_validator_state.json.backup
rm -rf $HOME/.mantrachain/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/mantra-testnet/ | egrep -o "> mantra-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/mantra-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.mantrachain
mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json

sudo systemctl restart mantrachaind && journalctl -u mantrachaind -f --no-hostname -o cat
```

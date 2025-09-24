## Snapshot
```
sudo systemctl stop namadad

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/mainnet/namada | egrep -o ">namada-snapshot.*\.tar.lz4" | tr -d ">")
cp $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/data/priv_validator_state.json $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/priv_validator_state.json.backup

rm -rf $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/data $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/{db,wasm}
curl https://snapshot.konsortech.xyz/mainnet/namada/$SNAP_NAME | lz4 -dc - | tar -xf - -C $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420

mv $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/priv_validator_state.json.backup $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/data/priv_validator_state.json
sudo systemctl restart namadad && sudo journalctl -fu namadad -o cat
```

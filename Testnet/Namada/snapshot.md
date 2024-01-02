## Snapshot
```
sudo apt install lz4
sudo systemctl stop namadad
cd $HOME/.local/share/namada

wget https://snapshot.konsortech.xyz/namada/namada-snapshot-20240102.tar.lz4
cp $HOME/.local/share/namada/public-testnet-15.0dacadb8d663/cometbft/data/priv_validator_state.json /$HOME
rm -rf public-testnet-15.0dacadb8d663/db/
rm -rf public-testnet-15.0dacadb8d663/cometbft/data/

tar -xvf namadatestnet.tar.gz
cp $HOME/priv_validator_state.json $HOME/.local/share/namada/public-testnet-15.0dacadb8d663/cometbft/data/
sudo systemctl start namada.service && journalctl -fu namadad -o cat | ccze
```

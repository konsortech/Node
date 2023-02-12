## State Sync
```
sudo systemctl stop cored

cp $HOME/.core/coreum-testnet-1/data/priv_validator_state.json $HOME/.core/coreum-testnet-1/data/priv_validator_state.json.backup
cored tendermint unsafe-reset-all --home $HOME/.core/coreum-testnet-1/ --keep-addr-book

SNAP_RPC="https://coreum.rpc.konsortech.xyz/:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="1a3a573c53a4b90ab04eb47d160f4d3d6aa58000@coreum.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.core/coreum-testnet-1/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.core/coreum-testnet-1/config/config.toml

mv $HOME/.core/coreum-testnet-1/data/priv_validator_state.json.backup $HOME/.core/coreum-testnet-1/data/priv_validator_state.json

sudo systemctl restart cored
sudo journalctl -u cored -f --no-hostname -o cat
```

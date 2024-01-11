## State Sync
```
sudo systemctl stop artelad

cp $HOME/.artelad/data/priv_validator_state.json $HOME/.artelad/priv_validator_state.json.backup
artelad tendermint unsafe-reset-all --home $HOME/.artelad --keep-addr-book

SNAP_RPC="https://testnet-artela-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="7e583fda2efbc30c7a1ce13727320fc99c17a26d@testnet-artela.konsortech.xyz:42656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.artelad/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.artelad/config/config.toml

mv $HOME/.artelad/priv_validator_state.json.backup $HOME/.artelad/data/priv_validator_state.json

sudo systemctl restart artelad
sudo journalctl -u artelad -f --no-hostname -o cat
```

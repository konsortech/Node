## State Sync
```
sudo systemctl stop mande-chaind

cp $HOME/.mande-chain/data/priv_validator_state.json $HOME/.mande-chain/priv_validator_state.json.backup
mande-chaind tendermint unsafe-reset-all --home $HOME/.mande-chain --keep-addr-book

SNAP_RPC="https://testnet-mande-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="f011505f4eb1490bba56719dda171934b2ca0c9f@testnet-mande.konsortech.xyz:12656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.mande-chain/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.mande-chain/config/config.toml

mv $HOME/.mande-chain/priv_validator_state.json.backup $HOME/.mande-chain/data/priv_validator_state.json

sudo systemctl restart mande-chaind
sudo journalctl -u mande-chaind -f --no-hostname -o cat
```

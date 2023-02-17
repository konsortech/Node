## State Sync
```
sudo systemctl stop aurad

cp $HOME/.aura/data/priv_validator_state.json $HOME/.aura/priv_validator_state.json.backup
aurad tendermint unsafe-reset-all --home $HOME/.aura --keep-addr-book

SNAP_RPC="https://testnet-aura-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="31f11907b83ab12d86910ad0c9217ca62a5b8d5d@testnet-aura.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.aura/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.aura/config/config.toml

mv $HOME/.aura/priv_validator_state.json.backup $HOME/.aura/data/priv_validator_state.json

sudo systemctl restart aurad
sudo journalctl -u aurad -f --no-hostname -o cat
```

## State Sync
```
sudo systemctl stop oraid

cp $HOME/.oraid/data/priv_validator_state.json $HOME/.oraid/priv_validator_state.json.backup
oraid tendermint unsafe-reset-all --home $HOME/.oraid --keep-addr-book

SNAP_RPC="https://mainnet-orai-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="efb9d22a6fdf7460f965982ae013d242bbbfd53c@mainnet-orai.konsortech.xyz:33656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.oraid/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.oraid/config/config.toml

mv $HOME/.oraid/priv_validator_state.json.backup $HOME/.oraid/data/priv_validator_state.json

sudo systemctl restart oraid
sudo journalctl -u oraid -f --no-hostname -o cat
```

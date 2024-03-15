## State Sync
```
sudo systemctl stop wardend

cp $HOME/.warden/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json.backup
wardend tendermint unsafe-reset-all --home $HOME/.warden --keep-addr-book

SNAP_RPC="https://testnet-warden-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

PEERS="00c0b45d650def885fcbcc0f86ca515eceede537@testnet-warden.konsortech.xyz:15656"
sed -i -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.warden/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.warden/config/config.toml

mv $HOME/.warden/priv_validator_state.json.backup $HOME/.warden/data/priv_validator_state.json

sudo systemctl restart wardend
sudo journalctl -u wardend -f --no-hostname -o cat
```

## Snapshot
```
sudo systemctl stop haqqd
cp $HOME/.haqqd/data/priv_validator_state.json $HOME/.haqqd/priv_validator_state.json.backup
rm -rf $HOME/.haqqd/data

SNAP_NAME=$(curl -s https://snapshot.konsortech.xyz/mainnet/haqq/ | egrep -o ">iris-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot.konsortech.xyz/mainnet/haqq/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.haqqd
mv $HOME/.haqqd/priv_validator_state.json.backup $HOME/.haqqd/data/priv_validator_state.json

sudo systemctl restart haqqd && journalctl -u haqqd -f --no-hostname -o cat
```

## Snapshot
```
sudo systemctl stop paxid
cp ~/go/bin/paxi/data/priv_validator_state.json ~/go/bin/paxi/priv_validator_state.json.backup
rm -rf ~/go/bin/paxi/data

SNAP_NAME=$(curl -s https://snap1.konsortech.xyz/paxi/ | egrep -o ">paxi-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snap1.konsortech.xyz/paxi/${SNAP_NAME} | lz4 -dc - | tar -xf - -C ~/go/bin/paxi
mv ~/go/bin/paxi/priv_validator_state.json.backup  ~/go/bin/paxi/data/priv_validator_state.json

sudo systemctl restart paxid && journalctl -u paxid -f --no-hostname -o cat
```

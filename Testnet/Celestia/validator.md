# Usefull CLI guide for celestia light node 

## check wallet 
```
cd celestia-node
./cel-key list --node.type light --keyring-backend test --p2p.network blockspacerace
```
## check wallet balance 
```
curl -X GET http://127.0.0.1:26659/balance
```
## get node id 
```
AUTH_TOKEN=$(celestia light auth admin --p2p.network blockspacerace)
curl -s -X POST -H "Authorization: Bearer $AUTH_TOKEN" -H 'Content-Type: application/json' -d '{"jsonrpc":"2.0","id":0,"method":"p2p.Info","params":[]}' http://localhost:26658 | jq -r .result.ID
```
## check logs 
```
sudo journalctl -u celestia-lightd.service -f
```
## delete wallet
```
./cel-key delete wallet-old --keyring-backend test --node.type light --p2p.network blockspacerace
```
## create new wallet 
```
./cel-key add wallet --keyring-backend test --node.type light --p2p.network blockspacerace
```
## restore wallet
```
./cel-key add wallet --keyring-backend test --node.type light --p2p.network blockspacerace
```

## uninstal celestia light node 
```
cd $HOME
sudo systemctl stop celestia-lightd
sudo systemctl disable celestia-lightd
sudo rm /etc/systemd/system/celestia-lightd.service
sudo systemctl daemon-reload
rm -f $(which celestia-lightd)
rm -rf $HOME/.celestia-lightd
rm -rf $HOME/celestia-lightd
```

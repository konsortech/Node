# How to update near mainnet node 

## stop the node 
```
sudo systemctl stop neard
```
## change to nearcore directory 
```
cd nearcore
```
## Fetch and checkout last relesease tin this exampe i use version 1.31.0 
* you can find lates version here alwasy use stable version 
* be sure you update the node inside screen 
``` 
screen # and press enter
```
## Find lates release here
```
https://github.com/near/nearcore/releases
```
```
git fetch 
git checkout 1.31.1
make release
sudo systemctl start neard && journalctl -n 100 -f -u neard | ccze -A
```


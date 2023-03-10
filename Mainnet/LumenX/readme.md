### How To Install Full Node LumenX Mainnet

## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export LUMENX_CHAIN_ID=LumenX" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux net-tools -y
```

## Install go
```
if ! [ -x "$(command -v go)" ]; then
     ver="1.19.4"
     cd $HOME
     wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
     sudo rm -rf /usr/local/go
     sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
     rm "go$ver.linux-amd64.tar.gz"
     echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
     source ~/.bash_profile
fi
```

## Download and build binaries
```
cd $HOME
git clone https://github.com/cryptonetD/lumenx.git
git checkout v1.3.3
cd lumenx
make install
```

## Config app
```
lumenxd config chain-id $LUMENX_CHAIN_ID
lumenxd config keyring-backend file
```

## Init app
```
lumenxd  init $NODENAME --chain-id $LUMENX_CHAIN_ID
```

### Download configuration
```
wget -O $HOME/.lumenx/config/genesis.json https://raw.githubusercontent.com/cryptonetD/lumenx/master/config/genesis.json
```

## Set seeds and peers
```
sed -i 's/persistent_peers = \"\"/persistent_peers = \"bc22063df30a0644df742cdb2764b1004df6e3e3@node1.lumenex.io:26656,9cd5f77ac27254891f64801470b0c3432188c62c@node2.lumenex.io:26656,78669849476c8b728abe178475c6f016edf175cf@node3.lumenex.io:26656,48444a4bacc0cafa049d777152473769ab17c0c3@node4.lumenex.io:26656,3d99b79129adeebd237d4153cbba6a749e0ce489@node1.olivestory.co.kr:26656,8246854d88bbba7acec7b4d86c9b418c90816f1f@rpc.lumenx.indonode.net:24656,2c341d570e537683d23102e64e7b73f4bbaef829@65.21.201.244:26766,1d94c81f6b25a51be173d22523f6267113bfcbec@45.134.226.70:26656,39d8e366837505e3a31948d761cc08ac8ed4a44b@188.165.232.199:26666,9a49635f0ecb7ba93fc9eba952cbe58767557010@185.215.180.70:26656,e91a86a4bec23993f584f346208e7b47285eb632@65.21.226.230:27656,3b584334f64ab60f92388ea22bc870dcacf4c157@157.90.179.182:56656,43c4eb952a35df720f2cb4b86a73b43f682d6cb1@37.187.149.93:26696,3c7c6c284806053c21b0e0dbfd3ca59797eab1d7@65.108.7.44:51656,e3989262b8dff3596f3b1d5e44372e9326362552@192.99.4.66:26666,b9aee01d4a878d0cf6beff20cabc9d4659cdd441@65.108.44.100:27656\"/g' $HOME/.lumenx/config/config.toml
sed -i -e "s|^seeds *=.*|seeds = \"0da8132a62468581db52774e9a513eb032179edd@45.94.58.246:17656\"|" $HOME/.lumenx/config/config.toml
```

## Set Minimum Gas Price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0025ulumen\"|" $HOME/.lumenx/config/app.toml
```


## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.lumenx/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.lumenxd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.lumenxd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.lumenxd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.lumenxd/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/lumenxd.service > /dev/null << EOF
[Unit]
Description=LumenX Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lumenxd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable lumenxd
sudo systemctl restart lumenxd && sudo journalctl -u lumenxd -f -o cat
```

### How To Install Full Node Arable Protocol Mainnet

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
echo "export ACRE_CHAIN_ID=acre_9052-1" >> $HOME/.bash_profile
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
git clone https://github.com/ArableProtocol/acrechain.git
cd acrechain
git checkout v1.1.1
make install
```

## Config app
```
acred config chain-id $ACRE_CHAIN_ID
```

## Init app
```
acred  init $NODENAME --chain-id $ACRE_CHAIN_ID
```

### Download configuration
```
wget https://raw.githubusercontent.com/ArableProtocol/acrechain/main/networks/mainnet/acre_9052-1/genesis.json -O $HOME/.acred/config/genesis.json
```

## Set seeds and peers
```
PEERS="ef28f065e24d60df275b06ae9f7fed8ba0823448@46.4.81.204:34656,e29de0ba5c6eb3cc813211887af4e92a71c54204@65.108.1.225:46656,276be584b4a8a3fd9c3ee1e09b7a447a60b201a4@116.203.29.162:26656,e2d029c95a3476a23bad36f98b316b6d04b26001@49.12.33.189:36656,1264ee73a2f40a16c2cbd80c1a824aad7cb082e4@149.102.146.252:26656,dbe9c383a709881f6431242de2d805d6f0f60c9e@65.109.52.156:7656,d01fb8d008cb5f194bc27c054e0246c4357256b3@31.7.196.72:26656,91c0b06f0539348a412e637ebb8208a1acdb71a9@178.162.165.193:21095,bac90a590452337700e0033315e96430d19a3ffa@23.106.238.167:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.acred/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.acred/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="19"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.acred/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.acred/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.acred/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.acred/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/acred.service > /dev/null << EOF
[Unit]
Description=Acre Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which acred) start
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
sudo systemctl enable acred
sudo systemctl restart acred && sudo journalctl -u acred -f -o cat
```

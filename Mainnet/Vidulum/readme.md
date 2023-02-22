### How To Install Full Node Vidulum Mainnet

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
echo "export VIDULUM_CHAIN_ID=vidulum-1" >> $HOME/.bash_profile
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
git clone https://github.com/vidulum/mainnet
cd mainnet
make install
```

## Config app
```
vidulumd config chain-id $VIDULUM_CHAIN_ID
```

## Init app
```
vidulumd  init $NODENAME --chain-id $VIDULUM_CHAIN_ID
```

### Download configuration
```
wget https://raw.githubusercontent.com/vidulum/mainnet/main/genesis.json -O ${HOME}/.vidulum/config/genesis.json
```

## Set seeds and peers
```
SEEDS="883ec7d5af7222c206674c20c997ccc5c242b38b@ec2-3-82-120-39.compute-1.amazonaws.com:26656,eed11fff15b1eca8016c6a0194d86e4a60a65f9b@apollo.erialos.me:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$PEERS\"/" $HOME/.vidulum/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.vidulum/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.vidulum/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.vidulum/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.vidulum/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.vidulum/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/vidulumd.service > /dev/null << EOF
[Unit]
Description=Vidulum Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which vidulumd) start
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
sudo systemctl enable vidulumd
sudo systemctl restart vidulumd && sudo journalctl -u vidulumd -f -o cat
```

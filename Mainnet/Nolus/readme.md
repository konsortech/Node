### How To Install Full Node Nolus Mainnet

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
echo "export NOLUS_CHAIN_ID=pirin-1 >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux net-tools ccze -y
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
git clone https://github.com/nolus-protocol/nolus-core
cd nolus-core
git checkout v0.3.0
make install
```

## Config app
```
nolusd config chain-id $NOLUS_CHAIN_ID
```

## Init app
```
nolusd init $NODENAME --chain-id $NOLUS_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -s https://raw.githubusercontent.com/nolus-protocol/nolus-networks/main/mainnet/pirin-1/genesis.json > $HOME/.nolus/config/genesis.json
```

## Set seeds and peers
```
SEEDS=""
PEERS="b84ca6b00682bf80052f0d544872c78614f84d3d@mainnet-nolus:12656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.nolus/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.nolus/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "defult"|g' $HOME/.nolus/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.nolus/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "10"|g' $HOME/.nolus/config/app.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001unls"|g' $HOME/.nolus/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/nolusd.service > /dev/null << EOF
[Unit]
Description=Nolus Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which nolusd) start
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
sudo systemctl enable nolusd
sudo systemctl restart nolusd && sudo journalctl -u nolusd -f -o cat
```

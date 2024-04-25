### How To Install Full Node Side Protocol Testnet

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
echo "export SIDE_CHAIN_ID=side-testnet-3	" >> $HOME/.bash_profile
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
  ver="1.21.9"
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
rm -rf sidechain
git clone https://github.com/sideprotocol/sidechain.git
cd sidechain
git checkout v0.7.0
make install
```

## Config app
```
sided config chain-id $SIDE_CHAIN_ID
```

## Init app
```
sided init $NODENAME --chain-id $SIDE_CHAIN_ID
```

### Download configuration
```
cd $HOME
wget -O $HOME/.side/config/genesis.json "https://snap1.konsortech.xyz/side-testnet/genesis.json"
wget -O $HOME/.side/config/addrbook.json "https://snap1.konsortech.xyz/side-testnet/addrbook.json"
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.side/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.side/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.side/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "19"|g' $HOME/.side/config/app.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.005uside"|g' $HOME/.side/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/sided.service > /dev/null << EOF
[Unit]
Description=Side Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which sided) start
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
sudo systemctl enable sided
sudo systemctl restart sided && sudo journalctl -u sided -f -o cat
```

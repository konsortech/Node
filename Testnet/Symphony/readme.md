### How To Install Full Node Symphony Testnet

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
echo "export SYMPHONY_CHAIN_ID=symphony-testnet-2" >> $HOME/.bash_profile
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
  ver="1.22.4"
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
rm -rf symphony
git clone https://github.com/Orchestra-Labs/symphony symphony
cd symphony
git checkout 0.2.1
make install
```

## Init app
```
symphonyd init $NODENAME --chain-id $SYMPHONY_CHAIN_ID
```

### Download configuration
```
cd $HOME
wget -O $HOME/.symphonyd/config/genesis.json https://snap2.konsortech.xyz/symphony/genesis.json
wget -O $HOME/.symphonyd/config/addrbook.json https://snap2.konsortech.xyz/symphony/addrbook.json
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.symphonyd/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.symphonyd/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.symphonyd/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "19"|g' $HOME/.symphonyd/config/app.toml
```

## Set minimum gas price
```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.005uslf"|g' $HOME/.symphonyd/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/symphonyd.service > /dev/null << EOF
[Unit]
Description=Symphony Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which symphonyd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable symphonyd
sudo systemctl restart symphonyd && sudo journalctl -u symphonyd -f -o cat
```

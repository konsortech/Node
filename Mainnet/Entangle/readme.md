### How To Install Full Node Entangle Mainnet

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
echo "export ENTANGLE_CHAIN_ID=entangle_33033-1" >> $HOME/.bash_profile
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
  ver="1.21.2"
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
git clone https://github.com/Entangle-Protocol/entangle-blockchain
cd entangle-blockchain/
make install
```

## Config app
```
entangled config chain-id $ENTANGLE_CHAIN_ID
entangled config keyring-backend file
```

## Init app
```
entangled init $NODENAME --chain-id $ENTANGLE_CHAIN_ID
```

### Download configuration
```
wget -O $HOME/.entangled/config/genesis.json https://snapshot.konsortech.xyz/entangle/genesis.json
wget -O $HOME/.entangled/config/addrbook.json https://snapshot.konsortech.xyz/entangle/addrbook.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"ce585ba3e81c0064da87ca6376a37fc8a57469ed@mainnet-seed.konsortech.xyz:15165\"|" $HOME/.entangled/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.entangled/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.entangled/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.entangled/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "10"|g' $HOME/.entangled/config/app.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "10aNGL"|g' $HOME/.entangled/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/entangled.service > /dev/null << EOF
[Unit]
Description=Entangle Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which entangled) start --chain-id $ENTANGLE_CHAIN_ID
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
sudo systemctl enable entangled
sudo systemctl restart entangled && sudo journalctl -u entangled -f -o cat
```

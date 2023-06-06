### How To Install Full Node Composable-Network Testnet-2

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
echo "export COMPOSABLE_CHAIN_ID=banksy-testnet-2" >> $HOME/.bash_profile
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
  ver="1.18.2"
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
git clone https://github.com/notional-labs/composable-testnet
cd composable-testnet 
git checkout v2.3.3-testnet2fork
make install
```

## Config app
```
banksyd init NODE_NAME --chain-id banksy-testnet-2
```

## Init app
```
banksyd init $NODENAME --chain-id $COMPOSABLE_CHAIN_ID
```

### Download configuration
```
cd $HOME
wget -O $HOME/.banksy/config/genesis.json "wget -O ~/.banksy/config/genesis.json https://raw.githubusercontent.com/notional-labs/composable-networks/main/testnet-2/genesis.json"
```

## Set seeds and peers
```
seeds="872c8a78a17a24d6f44e1126c46ef52069c7bb18@65.109.80.150:2630,5c2a752c9b1952dbed075c56c600c3a79b58c395@composable-testnet-seed.autostake.com:26976,20e1000e88125698264454a884812746c2eb4807@seeds.lavenderfive.com:22256,3f472746f46493309650e5a033076689996c8881@composable-testnet.rpc.kjnodes.com:15959,ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:22256,945e8384ea51c5c6f7b9a90df8d8da120516d897@rpc.composable-t.indonode.net:47656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.banksy/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.banksy/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.banksy/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.banksy/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "17"|g' $HOME/.banksy/config/app.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0upica"|g' $HOME/.banksy/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/banksyd.service > /dev/null << EOF
[Unit]
Description=Composable Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which banksyd) start  --home /root/.banksy
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
sudo systemctl enable banksyd
sudo systemctl restart banksyd && sudo journalctl -u banksyd -f -o cat
```

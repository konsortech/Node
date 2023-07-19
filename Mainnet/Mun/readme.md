### How To Install Full Node MUN Blockchain Mainnet

## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your-Nodename-Moniker>
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export MUN_CHAIN_ID=mun-1" >> $HOME/.bash_profile
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
  ver="1.20.4"
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
git clone https://github.com/munblockchain/mun-node
cd mun-node
make install
```

## Config app
```
mund config chain-id $MUN_CHAIN_ID
```

## Init app
```
mund init $NODENAME --chain-id $MUN_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl https://mainnet1rpc.mun.money/genesis | jq ".result.genesis" > ~/.mun/config/genesis.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"036c564e3de76ffad3e013bea52c16eb1de5a400@31.14.40.112:26656\"|" $HOME/.mun/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.mun/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.mun/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.mun/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.mun/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.mun/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0umun\"|" $HOME/.mun/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/mund.service > /dev/null <<EOF
[Unit]
Description=MUN
After=network-online.target

[Service]
User=$USER
ExecStart=$(which mund) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable mund
sudo systemctl restart mund && sudo journalctl -u mund -f -o cat
```

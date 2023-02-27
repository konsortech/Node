### How To Install Full Node Gravity Bridge Mainnet

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
echo "export GRAVITY_CHAIN_ID=gravity-bridge-3" >> $HOME/.bash_profile
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
mkdir gravity-bin && cd gravity-bin
wget https://github.com/Gravity-Bridge/Gravity-Bridge/releases/download/v1.8.1/gravity-linux-amd64
mv gravity-linux-amd64 gravity
wget https://github.com/Gravity-Bridge/Gravity-Bridge/releases/download/v1.8.1/gbt
chmod +x *
sudo mv * /usr/bin/
```

## Init app
```
gravity init $NODENAME --chain-id $GRAVITY_CHAIN_ID
```

### Download configuration
```
wget https://raw.githubusercontent.com/Gravity-Bridge/gravity-docs/main/genesis.json
mv genesis.json $HOME/.gravity/config/genesis.json
wget -O "$HOME/addrbook.json" https://snapshots.polkachu.com/addrbook/gravity/addrbook.json
mv "$HOME/addrbook.json" ~/.gravity/config
```

## Set seeds and peers
```
seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:14256,86bd5cb6e762f673f1706e5889e039d5406b4b90@gravity.seed.node75.org:10556"
peers="73e27e9b376d2f58d80e29e8175542cb01c3024d@135.181.73.170:26856,ef9748625b4739c5411e276cf2cb0d2742a037f9@54.36.63.85:26656,39490daffac0c7847b0d2617e412b2942055a82b@95.214.53.46:26656,906114620df87a270b89404fdc7f15b3760fa34e@95.214.53.27:42656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.gravity/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.gravity/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.gravity/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.gravity/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "50"|g' $HOME/.gravity/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ugraviton\"/" $HOME/.gravity/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/gravity.service > /dev/null <<EOF
[Unit]
Description=gravity
After=network-online.target

[Service]
User=$USER
ExecStart=$(which gravity) start --home $HOME/.gravity
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
sudo systemctl enable gravity
sudo systemctl restart gravity && sudo journalctl -u gravity -f -o cat
```

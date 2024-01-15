### How To Install Full Node Blockx Network Mainnet

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
echo "export BLOCKX_CHAIN_ID=blockx_100-1" >> $HOME/.bash_profile
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
git clone https://github.com/BlockXLabs/BlockX-Genesis-Mainnet1.git
cd BlockX-Genesis-Mainnet1
make install
```

## Config app
```
blockxd config chain-id $BLOCKX_CHAIN_ID
```

## Init app
```
blockxd init $NODENAME --chain-id $BLOCKX_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls https://snapshot.konsortech.xyz/blockx/genesis.json > $HOME/.blockxd/config/genesis.json
curl -Ls https://snapshot.konsortech.xyz/blockx/addrbook.json > $HOME/.blockxd/config/addrbook.json
```

## Set seeds and peers
```
sed -i 's/seeds = \"\"/seeds = \"91c24aac8da5df96435b2af2984a6a5b5b00b54e@mainnet-seed.konsortech.xyz:39156\"/g' $HOME/.blockxd/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.blockxd/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.blockxd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.blockxd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.blockxd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.blockxd/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0abcx\"|" $HOME/.blockxd/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/blockxd.service > /dev/null <<EOF
[Unit]
Description=BlockX
After=network-online.target

[Service]
User=$USER
ExecStart=$(which blockxd) start --home /root/.blockxd
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
sudo systemctl enable blockxd
sudo systemctl restart blockxd && sudo journalctl -u blockxd -f -o cat
```

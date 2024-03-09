### How To Install Full Node Hedge Testnet

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
echo "export HEDGE_CHAIN_ID=berberis-1" >> $HOME/.bash_profile
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
  ver="1.21.5"
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
wget -O hedged https://github.com/hedgeblock/testnets/releases/download/v0.1.0/hedged_linux_amd64_v0.1.0
chmod +x hedged
mv hedged /root/go/bin
```

## Config app
```
hedged config chain-id $HEDGE_CHAIN_ID
```

## Init app
```
hedged init $NODENAME --chain-id $HEDGE_CHAIN_ID
hedged config chain-id $HEDGE_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -s https://snapshot3.konsortech.xyz/hedge/genesis.json > $HOME/.hedge/config/genesis.json
curl -s https://snapshot3.konsortech.xyz/hedge/addrbook.json > $HOME/.hedge/config/addrbook.json
```

## Set seeds and peers
```
seeds="c849fce4317af1a7710c55c586cbd1c18a557d2f@testnet-seed.konsortech.xyz:15165"
peers="b5d5226ac957b8b384644e0aa2736be4b40f806c@testnet-hedge.konsortech.xyz:14656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.hedge/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.hedge/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.hedge/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.hedge/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.hedge/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.hedge/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0025uhedge\"|" $HOME/.hedge/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/hedged.service > /dev/null <<EOF
[Unit]
Description=Hedge Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which hedged) start --home $HOME/.hedge
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
sudo systemctl enable hedged
sudo systemctl restart hedged && sudo journalctl -u hedged -f -o cat
```

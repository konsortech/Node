### How To Install Full Node Union Testnet

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
echo "export UNION_CHAIN_ID=union-testnet-6" >> $HOME/.bash_profile
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
  ver="1.21.4"
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
wget -O uniond https://snapshot.konsortech.xyz/union/uniond-v0.19.0-linux-amd64
chmod +X uniond
sudo mv uniond /root/go/bin
```

## Config app
```
alias uniond='uniond --home=$HOME/.union/'
uniond config chain-id $UNION_CHAIN_ID
```

## Init app
```
uniond init $NODENAME --chain-id $UNION_CHAIN_ID
```

### Download configuration
```
curl -Ls https://snapshot.konsortech.xyz/union/genesis.json > $HOME/.union/config/genesis.json
curl -Ls https://snapshot.konsortech.xyz/union/addrbook.json > $HOME/.union/config/addrbook.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"0cdeec25dd52cc48c3b52e990fd80ba5f9839162@testnet-seed.konsortech.xyz:39165\"|" $HOME/.union/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.union/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.union/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.union/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.union/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.union/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0muno\"|" $HOME/.union/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/uniond.service > /dev/null <<EOF
[Unit]
Description=Union Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which uniond) start --home $HOME/.union
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
sudo systemctl enable uniond
sudo systemctl restart uniond && sudo journalctl -u uniond -f -o cat
```

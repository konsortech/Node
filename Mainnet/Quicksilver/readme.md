### How To Install Full Node Quicksilver Mainnet

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
echo "export QUICKSILVER_CHAIN_ID=quicksilver-2" >> $HOME/.bash_profile
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
rm -rf quicksilver
git clone https://github.com/ingenuity-build/quicksilver.git
cd quicksilver
git checkout v1.2.17
make install
```

## Config app
```
quicksilverd config chain-id $QUICKSILVER_CHAIN_ID
```

## Init app
```
quicksilverd init $NODENAME --chain-id $QUICKSILVER_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls https://snapshot2.konsortech.xyz/quicksilver/genesis.json > $HOME/.quicksilverd/config/genesis.json
curl -Ls https://snapshot2.konsortech.xyz/quicksilver/addrbook.json > $HOME/.quicksilverd/config/addrbook.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"61e601946a69bf667f41d67a3515da0cf8950516@mainnet-seed.konsortech.xyz:46165\"|" $HOME/.quicksilverd/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.quicksilverd/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="19"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.quicksilverd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.quicksilverd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.quicksilverd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.quicksilverd/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0001uqck\"|" $HOME/.quicksilverd/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/quicksilverd.service > /dev/null <<EOF
[Unit]
Description=Quicksilver Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which quicksilverd) start
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
sudo systemctl enable quicksilverd
sudo systemctl restart quicksilverd && sudo journalctl -u quicksilverd -f -o cat
```

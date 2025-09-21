### How To Install Full Node haqqd Mainnet



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
echo "export HAQQ_CHAIN_ID=haqq_11235-1" >> $HOME/.bash_profile
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
git clone https://github.com/haqq-network/haqq.git
cd haqq
git checkout v1.8.5
make install
```

## Config app
```
haqqd config chain-id $HAQQ_CHAIN_ID
```

## Init app
```
haqqd init $NODENAME --chain-id $HAQQ_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -o ~/.haqqd/config/genesis.json https://snapshot.konsortech.xyz/mainnet/haqq/genesis.json
curl -o ~/.haqqd/config/addrbook.toml https://snapshot.konsortech.xyz/mainnet/haqq/addrbook.json
```

## Set seeds and peers
```
SEEDS="c839460bb2eac533e7e402f03065728df98f4e01@mainnet-seed.konsortech.xyz:11165"
PEERS="28498a11b7e3bdf7e63fb6c57b07cfcbe89d6a20@mainnet-haqq.konsortech.xyz:21656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.haqqd/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.haqqd/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.haqqd/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0aISLM\"|" $HOME/.haqqd/config/app.toml

```

## Create service
```
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=islamic
After=network-online.target

[Service]
User=$USER
ExecStart=$(which haqqd) start --home $HOME/.haqqd
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
sudo systemctl enable haqqd
sudo systemctl restart haqqd && sudo journalctl -u haqqd -f -o cat
```

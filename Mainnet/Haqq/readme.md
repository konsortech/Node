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
git checkout v1.4.0
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
curl -Ls https://snapshots.nodestake.top/haqq/genesis.json > $HOME/.haqqd/config/genesis.json
```

## Set seeds and peers
```
SEEDS=""
PEERS="83d80ab596c2b3a026b1f37ce3bd8310778f8c57@5.78.57.135:26656,9578a7c58cd91724c639aaf2ff5a01a35ce6e705@34.91.100.34:26656,9295e4898f83139726f3c5dd87d73efcd328e8f2@148.113.143.171:26656,ecb2274dbef5350270d3e21175b31746350cdc37@34.141.202.93:26656,977238095690cbbc57e2299f737c4258e0547cbd@34.107.55.236:26656,4e1c2471efb89239fb04a4b75f9f87177fd91d00@134.65.195.37:26656,fb0b8dfc6d81be517e8150f532c5d5ef09e5c898@34.88.105.219:26656,e345b6d6db937518aca00afb91c9e63e55b4fac1@135.181.183.212:26656,97e4468ac589eac505a800411c635b14511a61bb@169.155.46.251:26656,e04d814cf820c498e64153c27b021be1a70b6f6b@65.109.33.48:25656,4dddc284c0aa3373b3f73da95e989a607f37ee26@35.204.156.167:26656"
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

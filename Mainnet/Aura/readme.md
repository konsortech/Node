### How To Install Full Node Aura Network Testnet

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
echo "export AURA_CHAIN_ID=xstaxy-1" >> $HOME/.bash_profile
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
git clone --branch aura_v0.4.4 https://github.com/aura-nw/aura
cd aura && make
```

## Config app
```
aurad config chain-id $AURA_CHAIN_ID
```

## Init app
```
aurad init $NODENAME --chain-id $AURA_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -s https://raw.githubusercontent.com/aura-nw/mainnet-artifacts/main/xstaxy-1/genesis.json > $HOME/.aura/config/genesis.json

jq -S -c -M '' $HOME/.aura/config/genesis.json | sha256sum
# this should return
# 90b9404d38167e3b40f56ddc11a1565f0107b89008742425e44905871699febc  -
```

## Set seeds and peers
```
seeds="22a0ca5f64187bb477be1d82166b1e9e184afe50@18.143.52.13:26656,0b8bd8c1b956b441f036e71df3a4d96e85f843b8@13.250.159.219:26656"
peers="ced3a13f4f7200ce1a2392a5738c88532f794359@mainnet-aura.konsortech.xyz:25656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.bcna/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.aura/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.aura/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.aura/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.aura/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.aura/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.001uaura\"|" $HOME_PATH/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/aurad.service > /dev/null <<EOF
[Unit]
Description=aura
After=network-online.target

[Service]
User=$USER
ExecStart=$(which aurad) start --home $HOME/.aura
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
sudo systemctl enable aurad
sudo systemctl restart aurad && sudo journalctl -u aurad -f -o cat
```

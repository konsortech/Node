### How To Install Full Node Mises Mainnet

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
echo "export MISES_CHAIN_ID=mainnet" >> $HOME/.bash_profile
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
cd $HOME
git clone https://github.com/mises-id/mises-tm/
cd mises-tm/
git checkout main
make install
```

## Config app
```
misestmd config chain-id $MISES_CHAIN_ID
```

## Init app
```
misestmd init $NODENAME --chain-id $MISES_CHAIN_ID
```

### Download configuration
```
curl https://e1.mises.site:443/genesis | jq .result.genesis > ~/.misestm/config/genesis.json
curl https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Mises/addrbook.json > $HOME/.misestm/config/addrbook.json
```

## Set seeds and peers
```
SEEDS_PEERS="1070b5c04c9b2af28aedf1b8cbeaf7e90b123464@rpc.gw.mises.site:36656"
sed -i.bak -E "s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"$SEEDS_PEERS\"|"  ~/.misestm/config/config.toml
```

## Disable indexing
```
indexer="null" && sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.misestm/config/config.toml
```

## Config pruning
```
pruning="custom" && \
pruning_keep_recent=100 && \
pruning_keep_every=0 && \
pruning_interval=10 && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.misestm/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.misestm/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.misestm/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.misestm/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/misestmd.service > /dev/null <<EOF
[Unit]
Description=Mises Daemon
After=network.target

[Service]
Type=simple
User=root
ExecStart=/root/go/bin/misestmd start  
Restart=on-abort

[Install]
WantedBy=multi-user.target

[Service]
LimitNOFILE=65535  
EOF
```

## Register and start service
```
sudo systemctl daemon-reload && sudo systemctl enable misestmd && sudo systemctl restart misestmd && sudo journalctl -u misestmd -f -o cat
```

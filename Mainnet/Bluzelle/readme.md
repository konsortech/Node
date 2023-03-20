### How To Install Full Node Bluzelle Mainnet

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
echo "export BLUZELLE_CHAIN_ID=bluzelle-8" >> $HOME/.bash_profile
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
ver="1.17"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```
## Install Ignite
```
curl https://get.ignite.com/cli! | bash
```


## Download and build binaries
```
cd $HOME
git clone https://github.com/bluzelle/bluzelle-public bluzelle
cd bluzelle
git checkout 7bc61cc3ffe0cc90228b10a4db11f678d1db1160
cd curium
go build curiumd ./cmd/curiumd/
sudo mv curiumd /usr/bin/
ignite chain serve -f -v
```

## Config app
```
curiumd config chain-id $BLUZELLE_CHAIN_ID
```

## Init app
```
curiumd init $NODENAME --chain-id $BLUZELLE_CHAIN_ID
```

### Download configuration
```
curl https://bluzelle-rpc.genznodes.dev/genesis | jq -r '.result.genesis' > genesis.json
mv -r genesis.json .curium/config/genesis.json
```

## Set seeds and peers
```
PEERS=d3150799a6be2561ed6df3e266264140a6e2514d@35.158.183.94:26656,ec45a9687a7aa8c3aeebe1d135d255c450e5ad02@13.57.179.7:26656,ecec40366517cafc9db0b638ebab28ad6344a2f4@18.143.156.117:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.curium/config/config.toml
```

## Create service
```
sudo tee /etc/systemd/system/curiumd.service > /dev/null <<EOF
[Unit]
Description=curiumd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which curiumd) start --home $HOME/.curium
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
sudo systemctl enable curiumd
sudo systemctl restart curiumd && sudo journalctl -fu curiumd -o cat
```

### How To Install Full Node Mars Protocol Testnet

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
echo "export MARS_CHAIN_ID=ares-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux net-tools -y
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
git clone https://github.com/mars-protocol/hub.git
cd hub
git checkout v1.0.0-rc7
make install
```

## Config app
```
marsd config chain-id $MARS_CHAIN_ID
marsd config keyring-backend test
```

## Init app
```
marsd init $NODENAME --chain-id $MARS_CHAIN_ID
```

### Download configuration
```
curl -s https://raw.githubusercontent.com/mars-protocol/networks/main/ares-1/genesis.json > $HOME/.mars/config/genesis.json
curl -s https://snapshots1-testnet.nodejumper.io/mars-testnet/addrbook.json > $HOME/.mars/config/addrbook.json
```

## Set seeds and peers
```
PEERS="37fc77cca6c945d12c6e54166c3b9be2802ad1e6@mars-testnet.nodejumper.io:30656,cebe0a3be105df1c5682bfcb9692b43bed8b4378@178.208.252.54:28656,648d3e69a428485fbd3bf221a9292d895ea656f0@159.69.5.164:15656,1a026f66b85594cf1d842c6f00f665f6d8baddf2@65.108.126.35:33656,098891dcc9d0d704ed5e52b19c491440b749c14b@38.242.243.64:26656,3986b72739988aff6fbba4c2792f185d42779f2e@194.163.160.1:18556,fe8d614aa5899a97c11d0601ef50c3e7ce17d57b@65.108.233.109:18556,e5577ecbf793ce92ce5993c4841a340a4c9db64b@65.108.204.119:46656,6fce5a4698bd88724e6c84e5e737828e221a4ebb@51.81.57.80:10556,4f23560a080541248db96a0a83dd37ffe0beedaf@65.109.85.226:7240,ed904f43cb324053542b7ac7d2b56f4da74c682c@164.92.95.49:27656,7ed60b9fdfc250a7a10bba3c539bce58c9533b5a@65.108.11.180:23656,44f2ce33780b591a046bfaff0a35f0332c44d1b8@95.217.35.186:60756,14ff7bc373e6ffc6978afa3c83c811638a8553a6@85.239.243.210:26656,47ca487f5eed3eb88423f23cb1bb160083adf02d@5.78.44.218:26656,5c2a752c9b1952dbed075c56c600c3a79b58c395@95.214.53.233:27056,c37098f7f3c8943432b673c311ddbe0e12a93ab3@65.21.3.243:27656,e26ac62d4b4339bd8863c59027583c1f9a085675@185.225.232.196:22656,eab3633d86f723e46d3244a95ec3e5a89fdbb51b@67.217.60.198:11124,d2ea30dd1dc0fcf7ca0c4f4bb8bc1b9f07bd6b05@65.109.19.93:27312,3179d6c8897bbe6cbc8be327faa0b5943b4c066d@65.109.85.221:7240,1f2628d8da99dafc22653bbc74440a2bfd5397c1@38.242.142.118:26656,7f614946315d781fec92baf8cd6475fa6fea482a@65.109.92.148:61356,ac691054f05476e67ba235cad99dced8b5b57a85@173.212.243.149:26656,e4662fe7ec1a724063fa10654da1581a722dba0b@138.2.95.245:20656,1b4c9d74ca45ff542e8213446e9b384b311d0bea@65.108.200.248:55556,a841d3e526089172867a73b709fd14e1d9fb87bd@65.108.231.124:22656,d387afb4fb00f6c16e6adaee596cf2f75b328146@136.243.88.91:7240,db6231aafffcd7dff070d76771a9b77dd3ac6521@85.173.113.198:27656,50c30cc77743dd2adc133f27a8896af015bf5c6d@91.107.242.217:26656,482b1509c492e075ae9b507d38a5ff710e5a598f@209.182.238.30:26656,e8d1a9688c01cdcb3288d8d175f6229487580478@118.68.125.181:20656,61699a47c1b540d2581edb40e65627cdb50e6019@65.108.140.220:28656,db01c56a1cf6d6c9fd8e90bbbb5807e39e186b02@85.239.234.222:20656,08076453488caf03c5d391edcc124b31c558ad23@65.109.85.225:7240,13066720a4fa7e84a2011580834a63c7c6bff59d@188.166.244.23:20656,2e5aa457c4e01015582a64c3ee29daf7e11c29f0@52.184.84.70:20656,a5b29b2751482f1a19317abe2086b547d3ef14fd@49.12.216.13:26656,e615fe1ed10a00ffc6e9911fd201cad557a60976@178.124.214.192:44656,5d2057e1f7a3f136a25a8ff0b6fb32940b5e786b@199.175.98.112:26656,643e745c800b97fb28565f7c077c8c67375dd9c7@65.108.244.233:26656,76af3311dd480b5f6d3cdf73d2b5c3887191e1d2@143.198.147.157:20656,931d82351a5b96a1e9838008636b98c6e6b530bc@65.108.225.158:18556,7c7f52bf26d5ec2dcc9e016c0f521e0b2fe77fcd@95.214.55.25:26656,2ecaf2f77b593ad137542724ca66ee44dcd48258@108.175.1.130:20656,3b13aa694d168cd8f5d0a35a3b3dc43927f6ffc3@167.172.77.18:20656,e17a62b746f6dc3a19a49887ba484306859c4beb@206.246.71.251:45656,e2be9d91ce6a4422621cf6d8d82f270683b618da@212.118.37.125:26656,9e6eac82887f7422bc49651f8ffda6bfd2848f53@74.208.244.144:20656,0d03b322852add71896c6bbf0010e68410b45ac3@37.187.144.187:32656,adc3f9a1af20dd3439c48548016b7716deac87f9@65.109.93.115:30656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.mars/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.mars/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="19"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.mars/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.mars/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.mars/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.mars/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0umars\"|" $HOME/.mars/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/marsd.service > /dev/null << EOF
[Unit]
Description=Mars Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which marsd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable marsd
sudo systemctl restart marsd && sudo journalctl -u marsd -f -o cat
```

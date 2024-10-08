### How To Install Full Node Hypersign Testnet

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
echo "export HYPERSIGN_CHAIN_ID=prajna-1" >> $HOME/.bash_profile
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
git clone https://github.com/hypersign-protocol/hid-node.git
cd hid-node
git checkout v0.2.0
make install
```

## Init app
```
hid-noded init $NODENAME --chain-id $HYPERSIGN_CHAIN_ID
```

### Download configuration
```
wget -qO $HOME/.hid-node/config/genesis.json "https://snapshot.konsortech.xyz/hypersign/genesis.json"
wget -qO $HOME/.hid-node/config/addrbook.json "https://snapshot.konsortech.xyz/hypersign/addrbook.json"
```

## Set seeds and peers
```
SEEDS="d8dad59bfabdad37d6455f5513956fc802732b69@testnet-seed.konsortech.xyz:27165"
PEERS="50ce7a4b00a363326e4e9f355ca82873744b4517@212.118.42.10:26656,8486f27b121dd25c802fe28f8b0fd6ba2accdba0@45.151.122.3:26656,77234414b2b057fa7201883316ea69490eb55a70@213.133.100.172:26656,ec855c80ec23d1fe41fc741db869581609cc8b52@192.168.3.29:26656,dbb3c0e236843d2f02d3a9a65d25fee8576831db@144.76.97.251:26656,f8d6f2cf1247b37337001e031084126da387f696@136.243.55.237:26656,b2ec1e52264efdd89183b595ddc877f2e023453c@62.171.184.126:26656,4f854558dda4c7b0ef0e0f0262d1417e47007e5f@66.45.246.166:26656,cd447352bc9ddf8d14483fd5959481e0041eda65@178.18.254.211:26656,c2c4b3990e555a1c838ea3cdfe15426fd0c8f6a6@154.12.228.93:26656,a34190ba699058d753f7bde2fa8c42b07b563ffe@192.168.48.1:26656,7b13f540715f8117de13341f1a5dd40957d8ace9@167.235.21.165:26656,a6fae68113f3246d960653574f68902103002318@167.235.102.45:26656,8860943e224d635aa94bfb2fc6ed3a5a3ff42971@190.2.146.152:26656,feda90ce3d07b384fde0f29677d60f21c5090c33@51.89.195.66:17656,daf233bd26ccc63ffcf47202d72b25089ec5c6b2@142.132.135.125:26656,ef4b86eeaabf8d1ade4278adf728f1fbd65ec833@141.95.103.138:26656,43c8782bf72967173e98c8b13943c7f18a4a0202@5.181.190.76:26656,177de381a63a179f2b88516e95cae5cae261f116@173.212.198.50:26656,4ada7263e263023214a15343e277116985c8b528@5.161.99.136:26656,76533e599a58f47c9aeca805294af8d7cf7a4c7f@195.201.110.169:26656,ff1ee030ab1ba7009a4d497f1b815a2053070139@109.123.242.217:26656,6b6796923c0675265866be182fe1c5400fe16283@192.168.1.14:26656,c5cbace43cfa0043fc8d85b3bd128b76e0872bfb@88.99.177.78:26656,3a3c85dd6911370bef69785989ff2545b9fe78cb@10.0.1.10:26656,cccc44f39832eaa9ae345fa92e47b553517765aa@176.9.121.109:26656,09e9fbe80bcbbcf888dedf928d70cc12918d7f48@81.31.197.120:26656,378cd59b57643a878f8fd66c6853587d4c1aac34@38.46.220.58:26656,f24522f9153e4abeabb3f9a214801d0a21b5d71a@95.217.106.215:26656,69e7ff3d6bc66e3f1e5f1d0794643be4ace556fc@81.0.247.152:26656,2186c4c3ebd9cb4916e35dd443e89d9335993890@142.132.136.106:26656,636977c240470721ae1096dcc405e538a7c27d56@148.251.43.226:26656,7ebd20716a06edfad4ebc969f49b9bdad05141c6@37.27.48.156:26656,85ebee239df7b78d4565a5c28c2412ded3e2f72c@95.217.110.39:26656,e7c8f8e8daac4eee629ea08dd528b25c05b195d1@65.108.238.61:26656,071ec8aa9e9f96189878e45419703250c9648bb2@10.2.0.4:26656,4aeb5149f35dafe890720490ee2790f582c52508@65.109.69.163:26656,5cc2d1faff16fed70ee332fe9a87081788fd64e7@192.168.70.132:26656,b0fee95a6b8c04a1b9d0656fce7b72dc810e8530@162.55.103.44:26656,2afff3043a4a338f483078f3c3ec7bbb36fbce0b@65.21.132.27:26656,df582f4d61b2d56cfdd7a8701b67750310b54414@10.160.0.7:26656,1230eda50373e9285797342dc017b7fb5c96a295@65.108.211.81:26656,775e5830fb90a285d9cd1a2431c855dc693ed0ab@37.27.15.94:26656,45a6a6e2790583bf4dee1a3b1e4dd24fb89911b3@65.109.104.118:26656,0c6758a3f4554bbc67da73993bbb697764c5c534@38.242.142.227:26656,e47821bd8ade6f9df9ceaf9ac899f6f3393b49f1@65.108.134.215:26656,a78f329d7854582c8b8224ff0c84f7a80d8eac20@185.249.227.7:26656,525e4aa4e6a10f000211bdff2a0e824b89ec8098@148.251.177.108:26656,c8ff7a6594818fbd708917933e7b1599b9d67629@65.109.39.223:26656,d879996c98e624918088d10e8ddf552fcfc5cd19@65.109.237.80:26656,06c434618db29a55eb59b3be0bf3870b89aa6a92@185.249.227.6:26656,a52d3842a60d893c667e42a393325c45e2ab477c@95.217.207.236:26656,5e4fc955b23ab00f6a07cb6d56e89aafac0c85ff@167.86.85.122:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.hid-node/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.hid-node/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="19"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.hid-node/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.hid-node/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.hid-node/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.hid-node/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uhid\"/" $HOME/.hid-node/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/hid-noded.service > /dev/null <<EOF
[Unit]
Description=hypersign
After=network-online.target

[Service]
User=$USER
ExecStart=$(which hid-noded) start --home $HOME/.hid-node
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
sudo systemctl enable hid-noded
sudo systemctl restart hid-noded && sudo journalctl -u hid-noded -f -o cat
```

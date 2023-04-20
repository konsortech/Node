### How To Install Full Node Defund Testnet Private 4

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
echo "export DEFUND_CHAIN_ID=orbit-alpha-1" >> $HOME/.bash_profile
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
rm -rf defund
git clone https://github.com/defund-labs/defund
cd defund
git checkout v0.2.6
make install
```

## Config app
```
defundd config chain-id $DEFUND_CHAIN_ID
defundd config keyring-backend test
```

## Init app
```
defundd init $NODENAME --chain-id $DEFUND_CHAIN_ID
```

### Download configuration
```
curl -s https://raw.githubusercontent.com/defund-labs/testnet/main/defund-private-4/genesis.json > ~/.defund/config/genesis.json
wget -qO $HOME/.defund/config/addrbook.json "https://raw.githubusercontent.com/konsortech/Node/main/Testnet/Defund/addrbook.json"
```



## Set seeds and peers
```
SEEDS = "9f92e47ea6861f75bf8a450a681218baae396f01@94.130.219.37:26656,f03f3a18bae28f2099648b1c8b1eadf3323cf741@162.55.211.136:26656,f8fa20444c3c56a2d3b4fdc57b3fd059f7ae3127@148.251.43.226:56656,70a1f41dea262730e7ab027bcf8bd2616160a9a9@142.132.202.86:17000,e47e5e7ae537147a23995117ea8b2d4c2a408dcb@172.104.159.69:45656,74e6425e7ec76e6eaef92643b6181c42d5b8a3b8@defund-testnet-seed.itrocket.net:443"
PEERS=f03f3a18bae28f2099648b1c8b1eadf3323cf741@162.55.211.136:26656,28f14b89d10992cff60cbe98d4cd1cf84b1d2c60@88.99.214.188:26656,a56c51d7a130f33ffa2965a60bee938e7a60c01f@142.132.158.4:10656,11dd3e4614218bf584b6134148e2f8afae607d93@142.132.231.118:26656,4eb0bef7997b87086c40766193d812479238187c@217.76.55.66:26656,a32570fc38ffbff20cd4cbf72b335f4ef810d017@65.21.105.44:26656,a9c52398d4ea4b3303923e2933990f688c593bd8@157.90.208.222:36656,2b76e96658f5e5a5130bc96d63f016073579b72d@51.91.215.40:45656,0d560c5dedc7415c45d9a9a6c8f4c4b69b0d31cc@65.108.8.55:26656,b70f57527cd282047dbe8c74ff6115b555156ca5@194.126.173.150:31656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:40656,f8093378e2e5e8fc313f9285e96e70a11e4b58d5@141.94.73.39:45656,72ab81b6ba22876fc7f868b58efecb05ffac9753@65.109.86.236:28656,b136caf667b9cb81de8c1858de300376d7a0ee0f@65.21.53.39:46656,a2483a05d1e4ea9cc9bba7a7a407dc501dfeee55@38.242.205.160:26656,86764a07a5cf35a3eb79981a65c9376675072a92@65.109.88.180:33656,955d9b23f6ddb8888ffd98602dcd579bf31a9bf7@212.90.120.42:26656,8ce02398652b4f4c953280ecd21949c4cf4a1414@167.86.105.64:26656,1732f1e6660337caec7bceba6ae8142a8dc79b17@144.91.94.137:26656,ed3fae93393b402b64e6b860462b8b6034399e37@85.25.137.11:26656,47a6af2b45c2a8af64b36b4730bfb3d0c91ed870@88.210.3.84:26656,46587f4e7fe8e23b0cdcc77ee4b787f73d96a56c@190.102.106.50:31656,15d8ef2ae89c727dff1930e8e78894e6cd810774@95.217.134.242:26656,e149e0490dfb5ffb4857ba7a9c07103ecb6813a8@65.109.200.67:26656,7df04198931e556de89a8400a52e4fe8fc8bdfe3@65.108.60.172:26656,28d0f4d4b9debc4547e8d7862672171e7b2f8764@135.181.111.161:26656,164e549bcddbfee83fe19810b645e80cac1b358d@65.109.12.79:26656,d9315e4d36a0d36e5228143ce65bb01a7ae98ad0@62.113.117.179:26656,514d7a0dc5c5ab4df2269e106f02554763a0cd69@88.210.9.169:26656,4758cb09f15174708880c0986bb0b57af4dc5d5d@135.181.208.169:27656,b8514550b391c9dc573625c67ef931a144e2b1ca@168.119.117.120:26656,a9c4e48255c73cf49ea0459ef89c9c0a9ce9de80@65.108.240.79:26656,c7617e0de4986c28be878833290197229b96b4f0@181.214.147.81:18656,d1ba0f8137413cdce81ffaea04f8f25d1d5f32b6@65.109.167.55:26656,2fb53d2509f3bd47fa26f39d2a2c81347c9046ef@65.108.214.39:26656,2a138efb5ef0638386af44c3df32ccdc8895b4d0@65.21.172.60:36656,02642efcb81f1ae92442ae03985deb57fa3b717f@65.108.209.237:26656,51cd7e6e26ecc55785181a6b2d47645174fe025e@65.108.110.23:40656,0f332b3b2e0013d3a52bcf0d85871e510628c90f@193.178.170.14:26656,d31d9801e7a021d287570b94ffcf27b91b0d9b66@217.76.55.74:26656,c1b574a8230bb51a6d1ae74071659ecdae1e968d@217.76.55.67:26656,24c472618cd25bb4b88a26b4aad6025eb88f481e@213.239.215.77:42656,6bcb7d5f9d0515f6e5d7f63b8ca5fb2df1fc9232@65.109.3.8:26656,89865c3be8ed26d0ed4fd13d7bdec576beac20b6@5.75.145.68:26656,fadb50dd153e127fbd56b7a4823beb355d4c103b@217.76.55.73:26656,9389cefdaa999eb81b93f4354d1077553ceb7a86@217.76.55.76:26656,98f5af6f76dcbe21c7ce9d6b9e00ed4c56853dfe@65.109.137.238:26656,1d28e2177c362fe2f032ba296c69142544d688f0@217.76.55.71:26656,86813c773d619e716d35702a3f646f849869b920@5.75.142.37:26656,20045ce5bdc8fbc356d82351305fe2f9f188a4b5@217.76.55.68:26656,88232417b05f9e1f3cd6ff9fa3296219d577dee4@185.144.99.73:26656,7029821868bbd6788b95bf1093ef8e9a85d73143@65.108.13.185:27060,5b6efed49f2d1d51a29a2a1fe5d40a5417aa8578@95.216.100.241:40656,f114c02efc5aa7ee3ee6733d806a1fae2fbfb66b@5.9.147.22:25656,5afcb5884900d343384c9fb717d3104ab28ee200@162.55.175.251:26656,74e6425e7ec76e6eaef92643b6181c42d5b8a3b8@65.108.231.124:18656,12029c7af734a0bd1628fc76070fb8f372ddb3c2@65.108.57.194:26656,e2cfc10ce9b87e7571c8cbdd7c7335cfc087aea5@65.108.58.10:26656,7cd35b5686ff3bf21f82f64b5f8ac14ff94d245b@173.212.229.41:26656,e3ddb054a619da2205b72b6de5aefbce2b451a0f@81.0.247.136:26656,9d98ffa5f8092368e6229efb1c2bc66e165af6c0@146.19.24.52:18656,e6e7b14c4b241d27191e214fa9b187db5d44cf4f@65.109.70.23:11256,0c46cabe345df4df80981a18dfadc4855ae04de0@178.20.45.72:26656,74db679159f72cd2dc76ff789b2c3db9cc0fda58@164.68.121.197:26656,b48fb7ede14b0e7e1531a34f021f5674e413d42b@81.16.237.150:27656,f6e31813b6fadc0df4ae69326f6a412a9005f90d@194.104.136.101:26656,f4a69f8e4501832a2530dfaf191fba925fb945b9@146.190.121.100:26656,630efd3361997ace2e72c9285bd9a91f3a8df7cf@23.88.71.247:28656,01b73409f0a44e9998af038259ce079af906c405@65.109.167.54:26656,bccd2003a7eb23008479c76427ac2c276160e09a@75.119.154.72:26656,e0791d994b99049a398e28005ab8f4151f772d60@95.214.55.155:15656,3b91d9feed8ee4dbddf53e2ea6a1d628df32e09b@65.109.167.56:26656,206bd41fec86ae650c30ded9a460cbc2619d83d5@45.85.250.108:31656,fe1fe3318b450201b19827bbdf9d5aeb9ae2b916@107.155.91.166:31656,00a1f33be03942daea99de285875f27ba947b707@84.46.243.1:26656,d76364d1c093523a19823ddceca077ab88e3656b@45.140.147.117:26656,f133b5fec6b3b45e544e17a0ef2cd02ff93c0fdb@188.34.154.116:26656,dca0e42d5d6838954ae08b5526c42a80c01d5538@159.69.74.237:26756,f858783158275330cde90c3026c365dfcd84b254@65.21.132.27:28186,a8f19ec6e056ddb81116c0614ef5f325d748f9be@154.12.251.27:26656,22f39b636c4984d658061788609305b3dc87f3ac@165.232.176.176:26656,d45d007633b82518764ab12fafa543c46c848e5d@88.99.213.25:40656,ad24fa713f19422cba774dd18aa6403e86d1e4b4@213.239.207.165:26756,cb25e2bbf0bad0ebba2a6fa0aff34c86bf6f0766@162.55.80.116:28656,e1b47180e9e467c5a8861c637d50d0e244a5c801@65.108.14.10:28656,0d0309e38ed041c6038cb7f1e25f63d99cc046a8@142.132.166.157:25656,fa991418db3fcbccf6a0d94db313e52b758fbad2@95.216.14.72:32656,75cccc67bc20e7e5429b80c4255ffe44ef24bc26@65.109.85.170:33656,da81aefc4d073f57d617c74c34a2fb2b68106dfa@37.157.255.110:26656,6854d36513081c77a24987ab66a436e29e3e5cfa@65.21.131.215:26576,47dbd5dcdd9fb3d0580fc421bfbd9163d159c032@212.33.229.66:26656,51c8bb36bfd184bdd5a8ee67431a0298218de946@141.94.175.219:26656,d9f1a0f399c8db62206edb2be29a313829fc8521@135.181.128.19:26656,77b3dcacd513f7f7fa1b0247d716f464ad61e94d@65.109.65.210:34656,2c57fc71ccb618acd7823422afaa78bffb8550cd@65.109.93.152:31256,cd9832469bc35afcf69f6032bfc814ce8c85db5d@95.216.211.97:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.defund/config/config.toml
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" $HOME/.defund/config/config.toml
```

## Disable indexing
```
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.defund/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.defund/config/app.toml
```

## Set minimum gas price
```
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0ufetf"/g' $HOME/.defund/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/defundd.service > /dev/null <<EOF
[Unit]
Description=defund
After=network-online.target

[Service]
User=$USER
ExecStart=$(which defundd) start --home $HOME/.defund
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
sudo systemctl enable defundd
sudo systemctl restart defundd && sudo journalctl -u defundd -f -o cat
```

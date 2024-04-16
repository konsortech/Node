## How to Run Massa Node

### Update and Install Dependencies
```
sudo apt-get update && apt-get upgrade -y
sudo apt-get install clang librocksdb-dev screen libssl-dev pkg-config curl git build-essential libclang-dev librocksdb-dev -y
```

### Add Environment Variable
```
echo "export PASSWORD=<input_your_password>" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Download Latest Binary
```
cd $HOME
wget https://github.com/massalabs/massa/releases/download/MAIN.2.1/massa_MAIN.2.1_release_linux.tar.gz
tar -xzvf massa_MAIN.2.1_release_linux.tar.gz && rm massa_MAIN.2.1_release_linux.tar.gz
```

### Create Running Script
```
sudo tee /root/massa/massa-node/run.sh > /dev/null <<EOF
#!/bin/bash
cd ~/massa/massa-node/
./massa-node -p $PASSWORD |& tee logs.txt
EOF
```

### Set Script for Executable
```
chmod +x /root/massa/massa-node/run.sh
```

### Create Daemon Services
```
sudo tee /etc/systemd/system/massad.service > /dev/null <<EOF
[Unit]
Description=Massa Node
After=network-online.target
[Service]
Environment="RUST_BACKTRACE=full"
User=$USER
ExecStart=/root/massa/massa-node/run.sh
Restart=always
RestartSec=3
[Install]
WantedBy=multi-user.target
EOF
```


### Start Node
```
systemctl daemon-reload 
systemctl enable massad 
systemctl restart massad && journalctl -fu massad -o cat
```

### Restore wallet operations
```
$HOME/massa/massa-client/massa-client -p $PASSWORD 
wallet_add_secret_keys <input_your_private_key_wallet>
```

### Start staking
```
node_start_staking <your_public_wallet_address>
```


### Buy Rolls
```
buy_rolls <your_public_wallet_address> 1 0
```
``

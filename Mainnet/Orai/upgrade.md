## Update to ASA-2024-007 security patch For Non Docker Users and Non Cosmovisor

Please Backup your node first including oraid binary and data to prevent Apphash error

### Download libwasmvm v1.5.2
```
wget https://github.com/oraichain/wasmvm/raw/v1.5.2/internal/api/libwasmvm.x86_64.so -O /usr/local/lib/libwasmvm.x86_64.so
```

### Download orai binary
```
cd $HOME
wget -O oraid https://github.com/oraichain/orai/releases/download/v0.41.7-ASA-2024-007/oraid-amd64
chmod +x oraid
sudo mv oraid $(which oraid)
```

### Stop Your Node
```
sudo service oraid stop
```

### Add new Enviroment variable for libwasmvm multilple version on Oraichain Services

nano /etc/systemd/system/oraid.service 

```
[Unit]
Description=Orai Network Node
After=network.target
[Service]
Type=simple
User=root
ExecStart=/root/go/bin/oraid start --home /root/.oraid
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="LD_LIBRARY_PATH=/usr/local/lib"
[Install]
WantedBy=multi-user.target
```

### Start Oraichain Services
```
sudo systemctl daemon-reload
sudo service oraid restart && journalctl -fu oraid -o cat
```

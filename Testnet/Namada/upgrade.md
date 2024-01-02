### Upgrade Namada

How to Upgrade to v0.28.2:
```
cd $HOME
wget https://github.com/anoma/namada/releases/download/v0.28.2/namada-v0.28.2-Linux-x86_64.tar.gz
tar -xzvf namada-v0.28.2-Linux-x86_64.tar.gz
cd namada-v0.28.2-Linux-x86_64
sudo mv namada* /usr/local/bin/
sudo systemctl restart namadad && sudo journalctl -fu namadad -o cat | ccze
```

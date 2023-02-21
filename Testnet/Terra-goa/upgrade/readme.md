# stop service
```
sudo systemctl stop atreidesd
```
# download update
```
wget https://github.com/terra-money/alliance/archive/refs/tags/v0.1.0-goa.tar.gz
```
# untar
```
tar -zxvf v0.1.0-goa.tar.gz 
```
# CD
```
cd alliance-0.1.0-goa
```
#install go 
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
# CD 
```
cd alliance-0.1.0-goa
```
#build
```
make build-alliance ACC_PREFIX=atreides
```
```
sudo mv /root/alliance-0.1.0-goa/build/atreidesd $(which atreidesd)
```
```
systemctl restart atreidesd && sudo journalctl -u atreidesd -f -o cat
```
# unjail 
```
atreidesd tx slashing unjail --broadcast-mode=block --from wallet --chain-id atreides-1 --fees="1000uatr" --gas="1000000" --gas-adjustment="1.15"
```

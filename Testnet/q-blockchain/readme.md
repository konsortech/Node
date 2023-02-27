# prepare move to another VPS
```
apt install docker-compose -y
```
```
sudo apt-get update && sudo apt install jq && sudo apt install apt-transport-https ca-certificates curl software-properties-common -y && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin && sudo apt-get install docker-compose-plugin -y
```
# Download and Installation of Q Blockchain Files
```
git clone https://gitlab.com/q-dev/testnet-public-tools
cd testnet-public-tools
rm -fr testnet-validator
```
# upload folder testnet validator ke VPS baru

# edit file .env rubah ke ip vps baru
```
cd testnet-validator
nano .env
```
# download snapshot
```
rm -rf /var/lib/docker/volumes/testnet-validator_testnet-validator-node-data/_data/geth/chaindata/
mkdir -p /var/lib/docker/volumes/testnet-validator_testnet-validator-node-data/_data/geth/chaindata/
cd /var/lib/docker/volumes/testnet-validator_testnet-validator-node-data/_data/geth/chaindata//
wget -O - https://snapshots.stakecraft.com/q-testnet_2023-02-26.tar | tar xf -
```
# stop node di vps lama 
```
cd testnet-public-tools/testnet-validator/
docker-compose down -v
```
# jalankan node di vps baru 
```
cd ~/testnet-public-tools/testnet-validator
docker-compose up -d
docker-compose logs -f --tail "100"
```

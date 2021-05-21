## Run a Celo node from source, on a fresh Ubuntu 20.04 server:

This is for people who just want to quickly spin up a Celo node on a VPS (or on their localhost), in order to test with MetaMask.

For this you will need you MetaMask stuff to fill in the `-http.corsdomain` variable.

This is currently running on a server instance with 2GB RAM + 1GB swap, 2 vCPU.

### Foundationa

Update and install pre-requisites:
```
sudo apt update
sudo apt install -y make gcc
```
Add a little swap if you need it
```
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo nano /etc/fstab
```
Append this to the file:
```
/swapfile swap swap defaults 0 0
```
Save, close and continue:
```
wget https://golang.org/dl/go1.16.4.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.16.4.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

### Build and run Celo

```
git clone https://github.com/celo-org/celo-blockchain.git
cd celo-blockchain
make geth

sudo nano /etc/systemd/system/celo.service
```
Paste this text (e.g. ctrl-shift-v):
```
[Unit]
Description=service to start Celo
After=network.target

[Service]
User=ubuntu
Type=simple
Restart=always
RestartSec=1s
WorkingDirectory=/home/ubuntu/
ExecStart=/home/ubuntu/celo-blockchain/build/bin/geth --http --http.addr 0.0.0.0 --http.corsdomain {insert your metamask corsdomain stuff here}

[Install]
WantedBy=default.target
```
Then save, exit and continue:
```
sudo systemctl enable /etc/systemd/system/celo.service
sudo systemctl start celo.service
```
Observe the status with:
```
sudo journalctl -fu celo.service
```

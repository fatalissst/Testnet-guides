#### Server preparation

```bash
apt update && apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
#### Install GO
```bash
ver="1.19.1"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```
#### Node installation
```bash
git clone https://github.com/archway-network/archway && cd archway
git checkout v1.0.0-rc.4
make install

archwayd init Name --chain-id constantine-3
```

```bash
wget -O $HOME/.archway/config/addrbook.json "https://ss-t.archway.nodestake.top/addrbook.json"

pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.archway/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.archway/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.archway/config/app.toml
```

```bash
tee /etc/systemd/system/archwayd.service > /dev/null <<EOF
[Unit]
Description=archwayd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which archwayd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


```bash
systemctl daemon-reload
systemctl enable archwayd
systemctl restart archwayd && journalctl -u archwayd -f -o cat
```

# 🚧🚧🚧 under construction

#### `This is how-to-update manual`

# Upd. 24.05.2024 v2024.5-ragusa

> **NM** - Nym node, mode Mixnode
> **GW** - Nym node, mode Gateway

<!-- ################################################################
> **NR** - Nym node, mode Network Requestor    
################################################################ -->

#### Nym binaries
https://github.com/nymtech/nym/releases/tag/nym-binaries-v2024.5-ragusa

____


### Update and install tools
```
sudo apt update && sudo apt upgrade -y
```
```
sudo dpkg --configure -a
sudo apt install curl wget ufw make clang pkg-config libssl-dev build-essential git jq htop
```

### Install or update Rust
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
rustup update
```

#

### 🟠 NM UPDATING
### Build nym-node
```bash
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v2024.5-ragusa
cargo build --release --bin nym-node
```
<!--
git checkout release/ v 1_1_15
-->


### After build:
#### We need to update the version in the config
```bash
export VERSION="1.1.35"
read -p " Enter your mixnode id: " node_id
echo -e " You entered: \033[92m$node_id\033[0m"
sleep 0.5
sed -i "s/^version = .*/version = '$VERSION'/" $HOME/.nym/mixnodes/$node_id/config/config.toml && \
echo -e "\033[92m Mixnode version updated successfully \033[0m" || \
echo -e "\033[31m Failed to update mixnode version \033[0m"
```


### Replace and restart
```
sudo systemctl stop nym-mixnode && \
sudo mv ~/nym/target/release/nym-mixnode $(which nym-mixnode) && \
sudo systemctl start nym-mixnode && sudo journalctl -u nym-mixnode -f -o cat
```

### Change mixnode version to the v1.1.35 in the Nym Wallet (Bonding - Node Settings section)

#

### 🟢 **GW UPDATING**
### Build nym-node
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout master
cargo build --release --bin nym-gateway
```

### Change version to actual 1.1.33 in the config file
> #### How to. Enter name of your GW, for exmp. `gateway_name=my_gateway`    
> `gateway_name=`
```bash
# Open config and check

nano ~/.nym/gateways/$gateway_name/config/config.toml
```
> #### It should looks like
> ```bash
> # Version of the gateway for which this configuration was created.
> version = '1.1.33`
> ```

<!-- #####################################################################################
### Be sure the gateway config file contains `nymd urls`.
```
validator_nymd_urls = [

        'https://rpc.nyx.nodes.guru/',

]
```

 ### Remove `--enable-statistics` flag if it was in the GW service file
> Operators can switch to running GW in the standard mode which doesn't gather the amounts of data sent through them
```
nano /etc/systemd/system/nym-gateway.service
```
##################################################################################### -->

### Replace and restart
```bash
sudo systemctl stop nym-gateway && \
sudo mv ~/nym/target/release/nym-gateway $(which nym-gateway) && \
sudo systemctl daemon-reload && \
sudo systemctl start nym-gateway && sudo journalctl -u nym-gateway -f -o cat
```

### Change version to 1.1.33 in the Nym Wallet
Menu Bonding -> Gateway Settings    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett.jpg)    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett2.jpg)    

### 📌 No more need to unbond and rebond!
> The upgrade path for gateways now fixed to be like for mixnodes    

### 📌 Restart NR too after restarting GW

<!-- ############################# no more Rebond
#### Rebond gateway to v 1_1_13 in NYM wallet 
> Unbond. Stop GW, Start GW. Bond    
> *You will always need to rebond when upgrading gateways as this is how the network knows your gateway is available to be used*
##################################### -->

#

<!-- ######################################################################## S5 #################
### ⚫ S5
`...`


```bash
cd $HOME/nym
git pull
git checkout nym-binaries-v#############
cargo build --bin nym-socks5-client --release
# If nym-socks5-client runs as a service, stop it and then move 
# sudo systemctl stop nym-socks5-client
sudo mv target/release/nym-socks5-client /usr/local/bin/    

# Init socks5 client. Replace <socks5 client name> with your any name. It is a local identifier and it never transmitted over the network
nym-socks5-client init --id <socks5 client name> --provider <your service provider>
# for example
nym-socks5-client init --id my_socks5 --provider GegdtpNzYj4QCgpih9Kxv7ZVZxmVdxYHsDkiPsbT71XG.E8xtE8mrapjzFtyuziZSrsScAKhwZMH5wNpKWtKfzJ5Y@9Byd9VAtyYMnbVAcqdoQxJnq76XEg2dbxbiF5Aa5Jj9J --gateway 9Byd9VAtyYMnbVAcqdoQxJnq76XEg2dbxbiF5Aa5Jj9J    

# Start s5 client
cd $HOME/nym/target/release/nym-socks5-client
./nym-socks5-client run --id <socks5 client name>
```
#############################################################################  S5  ############## -->

<!-- ######################################################################## Service NR #################
### ⚫ S5
Service file for NR
```
[Unit]
Description=Nym Network Requester

[Service]
User=root
ExecStart=/usr/local/bin/nym-network-requester run --id ${nr_name}
KillSignal=SIGINT
Restart=on-failure
RestartSec=30
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```
#
#############################################################################  Service NR  ############## -->


<!-- INSTALLING NOTES

cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout master
cargo build --release --bin nym-mixnode

echo 'export node_id=nymsy' >> $HOME/.bash_profile

./nym-mixnode init --id $node_id --host $(curl -4 https://ifconfig.me)

./nym-mixnode sign --id $node_id --contract-msg .....FWRfgwetrTGEGer........


sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
sudo systemctl restart systemd-journald

sudo tee <<EOF >/dev/null /etc/systemd/system/nym-mixnode.service
[Unit]
Description=Mixnode_nymsy

[Service]
User=$USER
ExecStart=/usr/local/bin/nym-mixnode run --id '$node_id'
KillSignal=SIGINT
Restart=on-failure
RestartSec=5
StartLimitInterval=600
StartLimitBurst=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload

echo "DefaultLimitNOFILE=65535" | sudo tee -a /etc/systemd/system.conf

INSTALLING NOTES -->

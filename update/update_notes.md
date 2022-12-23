# Upd. 20.12.2022. New binaries 1.1.4
### Abbreviations:
- **NM** - Nym Mixnode
- **GW** - Gateway
- **NR** - Nym Network Requestor
- **NC** - Nym Client
- **S5** - Nym Socks5 Client    


### Nym binaries page
https://github.com/nymtech/nym/releases/tag/nym-binaries-v1.1.4
____

### Update and install tools
```
sudo apt update && sudo apt upgrade -y
```
```
sudo dpkg --configure -a
```
```
sudo apt install curl wget ufw make clang pkg-config libssl-dev build-essential git htop
```

### Install or update Rust
> Rust & cargo >= v1.56
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
```

### ⏬ First, clone Nym repository on each server where you will update. And then we will build each component
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git reset --hard
git pull
git checkout nym-binaries-v1.1.4
```
#

### 🟠 NM UPDATING
### Build NM
```bash
cd $HOME/nym
# Builds only binary, otherwise it's not compile right
cargo build --release --bin nym-mixnode
```
```
sudo systemctl stop nym-mixnode
sudo mv ~/nym/target/release/nym-mixnode /usr/local/bin/
```
```
sudo systemctl restart nym-mixnode && journalctl -u nym-mixnode -f -o cat
```
### After installation:
> #### Enter your mixnode name
> `node_id=YOUR_MIXNODE_NAME`
#### We need to update the version in the config. 2 ways
#### 1. Open config
```
nano ~/.nym/mixnodes/$node_id/config/config.toml
```
#### Change to new version `1.1.4`
```bash
[mixnode]
# Version of the NM for which this configuration was created.
version = '1.1.4'
```
#### 2. Or run *init* command
> #### Enter your wallet address
> `wallet=YOUR_WALLET_ADDRESS` For exmp. `wallet=n10lk93p495ywvmg50l80yhdzjea8zyslev8wz44`
```
nym-mixnode init --id $node_id --host $(curl ifconfig.me) --wallet-address $wallet
```
#### Be sure you have these `API url` in config file
`nano ~/.nym/mixnodes/$node_id/config/config.toml`    
```bash
# Addresses to APIs running on validator from which the node gets the view of the network.
validator_api_urls = ['https://validator.nymtech.net/api',

]
```
#### If there is empty string add it manually
#

### UPDATING NC, NR, GW

### 🟣 **NC**    
```
cd $HOME/nym
cargo build -p nym-client --release
```
#### Change version in config of the NC. (Replace *NAME_OF_YOUR_NC*)
`nano ~/.nym/clients/NAME_OF_YOUR_NC/config/config.toml`
<!-- nano ~/.nym/clients/grantee_cl/config/config.toml -->
```
sudo systemctl stop nym-client
sudo mv target/release/nym-client /usr/local/bin/
```
```
sudo systemctl daemon-reload
sudo systemctl enable nym-client
sudo systemctl restart nym-client
```

### 🔵 **NR**    
```
cd $HOME/nym
cargo build -p nym-network-requester
```
> No initialization required. But if the any of the processes are failing, then you might have to run init NR again: this will not overwrite keys or configs, so don't worry
```
sudo systemctl stop nym-network-requester
sudo mv target/debug/nym-network-requester /usr/local/bin/
```
```
sudo systemctl daemon-reload
sudo systemctl enable nym-network-requester
sudo systemctl restart nym-network-requester
```

### 🟢 **GW**    
```
cd $HOME/nym
cargo build -p nym-gateway --release
```
```bash
sudo systemctl stop nym-gateway
sudo mv target/release/nym-gateway /usr/local/bin/

# Restart
sudo systemctl restart nym-gateway
```
- #### Be sure the gateway config file contains `nymd urls`. 
```bash
# Replace NAME_OF_YOUR_GW to your GW name. Open config and check

nano ~/.nym/gateways/NAME_OF_YOUR_GW/config/config.toml
```
#### Must be
```
validator_nymd_urls = [

        'https://rpc.nyx.nodes.guru/',

]
```
- #### Change version to actual 1.1.4 in config of the GW
```bash
# Replace NAME_OF_YOUR_GW to your GW name. Open file and change to correct version
nano ~/.nym/gateways/NAME_OF_YOUR_GW/config/config.toml
```

- #### Rebond Gateway
#### Rebond gateway to v1.1.4 in NYM wallet (Unbond - Stop GW - Start GW - Bond)
> #### You will always need to rebond when upgrading gateways as this is how the network knows your gateway is available to be used    

#

### ⚫ S5
```bash
cd $HOME/nym
git pull
git checkout nym-binaries-v1.1.4
cargo build -p nym-socks5-client --release
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
  

# Just...

### Service file for NR
```
[Unit]
Description=Nym Network Requester

[Service]
User=root
ExecStart=/usr/local/bin/nym-network-requester run --enable-statistics
KillSignal=SIGINT
Restart=on-failure
RestartSec=30
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

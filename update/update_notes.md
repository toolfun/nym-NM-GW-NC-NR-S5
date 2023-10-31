<!-- ##################################################################
# Mixnode
binary v2023.3-kinder
version 1.1.32
config 1.1.32
wallet 1.1.32

# NR
binary v2023.3-kinder
version 1.1.31
config 1.1.31

# GW
binary v2023.3-kinder
version 1.1.31
config 1.1.31
wallet 1.1.31
################################################################## -->


#### `This is how-to-update manual`


# Upd. 31.10.2013 v2023.3-kinder
#### Abbreviations:
- **NM** - Nym Mixnode
- **GW** - Gateway
- **NR** - Nym Network Requestor
- **S5** - Nym Socks5 Client    


#### Nym binaries page
https://github.com/nymtech/nym/releases/tag/nym-binaries-v2023.3-kinder

____


### Update and install tools
```
sudo apt update && sudo apt upgrade -y
```
```
sudo dpkg --configure -a
sudo apt install curl wget ufw make clang pkg-config libssl-dev build-essential git htop
```

### Install or update Rust
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
rustup update
```

#

### 🟠 NM UPDATING
### Build NM
```bash
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v2023.3-kinder
cargo build --release --bin nym-mixnode
```
<!--
git checkout release/ v 1_1_15
-->


### After build:
#### We need to update the version in the config
```bash
export VERSION="1.1.32"
read -p " Enter your mixnode id: " node_id
echo -e " You entered: \033[92m $node_id \033[0m"
sleep 0.5
sed -i "s/^version = .*/version = '$VERSION'/" $HOME/.nym/mixnodes/$node_id/config/config.toml
echo -e "\033[92m Mixnode version updated \033[0m"
```

<!-- #########################################
#### Run init command
```bash
> read -p " Enter your mixnode id: " node_id
> echo -e " You entered: \033[92m $node_id \033[0m"
> sleep 0.5
> nym-mixnode init --id $node_id --host $(curl ifconfig.me)
> echo -e "\033[92m Mixnode version updated \033[0m"
> ```
######################################## -->

<!-- ###########################################################################################
> #### Enter your mixnode name
> `node_id=YOUR_MIXNODE_NAME`
> #### 1. Open config
> ```
> nano ~/.nym/mixnodes/$node_id/config/config.toml
> ```
> Change to current mixnode version `1.1.31`    
> ```bash
> [mixnode]
> # Version of the NM for which this configuration was created.
> version = '1.1.31'
> ```
> #### 2. Or run *init* command.

> Enter your wallet address, for example `wallet=n10lk93p495ywvmg50l80yhdzjea8zyslev8wz44`    
> `wallet=`
>
> ```
> nym-mixnode init --id $node_id --host $(curl ifconfig.me) --wallet-address $wallet
> ```


> ```
> nym-mixnode init --id $node_id --host $(curl ifconfig.me)
> ```

#### Be sure you have these `API url` in config file
`nano ~/.nym/mixnodes/$node_id/config/config.toml`    
```bash
# Addresses to APIs running on validator from which the node gets the view of the network.
nym_api_urls = [

        'https://validator.nymtech.net/api/',

]
```
> #### If there is empty string add it manually
######################################################################################### -->

### Replace and restart
```
sudo systemctl stop nym-mixnode && \
sudo mv ~/nym/target/release/nym-mixnode $(which nym-mixnode) && \
sudo systemctl start nym-mixnode && sudo journalctl -u nym-mixnode -f -o cat
```

### Change mixnode version to the v1.1.32 in the wallet app (Bonding - Node Settings section)

#

### UPDATING NC, NR, GW

### 🟣 ~**NC**~
> *Starting with v 1_1_13 the Nym Client is integrated with the Nym Network Requester*   
<!-- ############################
Build
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v
cargo build --release --bin nym-client
```
#### Change version in config of the NC
> #### Enter name of your NC, for exmp. `nym_client_name=my_client`    
`nym_client_name=`
```
nano ~/.nym/clients/$nym_client_name/config/config.toml
```
#### Init NC (init with or without --gateway flag, depends how you run it)
> #### Enter name of your NC, for exmp. `nym_client_name=my_client`. And enter your Gateway ID    
> `nym_client_name=`    
> `gateway_id=`
```
nym-client init --id $nym_client_name --gateway $gateway_id
```
```
sudo systemctl stop nym-client
sudo mv target/release/nym-client /usr/local/bin/
```
```
sudo systemctl restart nym-client
sudo journalctl -u nym-client -f -o cat
```
########################################### -->


### 🔵 **NR**
<!-- ########################################
> Since v 1_1_9 you *no longer* have to manually copy over the allowed.list.sample. On startup, the network requester will try and grab a 'default' whitelist from https://nymtech.net/.wellknown/network-requester/standard-allowed-list.txt    
> Update you allowed.list to include all of the domains on this list, as well as custom domains you may have.
> 
> Save your existing **allowed.list** before upgrading in case if Network Requester might overwrite your custom whitelists with the default one.    
> `$HOME/.nym/service-providers/network-requester/allowed.list`
######################################## -->

<!-- ######### OLD plan for the v 1_1_10 upgrade
Build    
Initialize    
Transfer NC data to NR
####### -->


### Build NR
```bash
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v2023.3-kinder
cargo build --release --bin nym-network-requester
```

<!-- ################################# OLD ## for the v 1_1_10 ###########################
#### Initiate the new NR
> Enter name for your NR, for exmp. `nr_name=my_nr`    
```
nr_name=
```
```
nym-network-requester init --id $nr_name
```

#### Copy the old keys from your NC to the NR configuration that was created with initiate command
> Enter name for your old NC, for exmp. `nc_name=my_nc`    
```
nc_name=
```
```
cp -r ~/.nym/clients/$nc_name/data/* ~/.nym/service-providers/network-requester/$nr_name/data
```

#### Edit configuration to match what you used on your NC. Specifically, edit the configuration file that `gateway_id, gateway_owner, gateway_listener` in the new NR config:
```
nano ~/.nym/service-providers/network-requester/$nr_name/config/config.toml
```
#### match those in the old NC config at:
```
nano ~/.nym/clients/$nc_name/config/config.toml
```
> *ExecStart=/usr/local/bin/nym-network-requester run --id $nr_name --enable-statistics*    

########################### END OLD ## for v 1_1_10 ############################### -->


### Edit configuration. Change version to the current 1.1.31 in the config file of the NR
> #### How to:
> Enter name for your NR, for exmp. `nr_name=my_nr`    
```
nr_name=
```
```
nano ~/.nym/service-providers/network-requester/$nr_name/config/config.toml
```
> #### It should looks like
> ```
> [client]
> # Version of the client for which this configuration was created.
> version = '1.1.31'
> ```

### Replace and restart
```
sudo systemctl stop nym-network-requester && \
sudo mv ~/nym/target/release/nym-network-requester $(which nym-network-requester) && \
sudo systemctl start nym-network-requester
sudo journalctl -u nym-network-requester -f -o cat
```

#

### 🟢 **GW**
### Build GW
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v2023.3-kinder
cargo build --release --bin nym-gateway
```

### Change version to actual 1.1.31 in the config file
> #### How to. Enter name of your GW, for exmp. `gateway_name=my_gateway`    
> `gateway_name=`
```bash
# Open config and check

nano ~/.nym/gateways/$gateway_name/config/config.toml
```
> #### It should looks like
> ```bash
> # Version of the gateway for which this configuration was created.
> version = '1.1.31`
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

### Change version to 1.1.31 in the Nym wallet
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

### ⚫ S5
`...`

<!-- ########################################
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
########################################### -->  

____


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


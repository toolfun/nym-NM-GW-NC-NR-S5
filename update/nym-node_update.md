#### `quick update manual`
#
<!-- ########################################################################################################## DEL
> 
> **NM** - Nym node: mixnode mode    
> **GW** - Nym node: gateway mode
########################################################################################################## DEL -->

> The current design requires working with root or sudo    
> Nym operator docs https://nymtech.net/operators    
> #### These are just notes on how to update. Please refer to the official documentation to install nym node


<!-- #############################
PRE-RELEASE : Nym Binaries v2025.2-hu 'latest'    
tag/nym-binaries-v2025.2-hu
# 
Changelog: https://nymtech.net/docs/operators/changelog
############################## -->


# 20.01.2025 Nym Binaries v2025.1-reeses `latest`
### 🎗 make new backup after the upgrade. there are config changes
Changelog: https://nymtech.net/docs/operators/changelog    

<!-- #############################
Key changes for operators:
"_We need everyone to upgrade as soon as they can. Thanks!_"
############################## -->


#

> ##### Update and install tools
> ```
> sudo apt update && sudo apt upgrade -y
> ```
> ```
> sudo dpkg --configure -a
> sudo apt install ufw git jq pkg-config libssl-dev build-essential ca-certificates
> ```

### Install or update Rust
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
rustup update
```

#

### UPDATING
#### BUILD or DOWNLOAD

- #### BUILD
#### Build nym-node
```bash
cd $HOME
rustup update
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
# git checkout master
git checkout nym-binaries-v2025.1-reeses
cargo build --release --bin nym-node
```

### Show builded binary info
```
~/nym/target/release/nym-node build-info
```

<!--
git checkout release/ v 1_1_15
-->

### Replace and restart
```bash
sudo systemctl stop nym-node && \
sudo mv ~/nym/target/release/nym-node $(which nym-node) && \
sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat
```
- ### DOWNLOAD
### Download nym-node binary
```sh
cd
NYM_VERSION='nym-binaries-v2025.1-reeses'
# download binary
wget https://github.com/nymtech/nym/releases/download/$NYM_VERSION/nym-node
chmod u+x nym-node
```

### Check downloaded binary info
```
./nym-node build-info
```

### Restart with the new version
```
cd
# stop nym-node
sudo systemctl stop nym-node
# backup current binary
cp $(which nym-node) nym-node-backup
# restart with the new binary
sudo mv ~/nym-node $(which nym-node) && sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat -n 50
```


<!-- ########################################################################################################## TO DELETE -----------------------------------
### Change mixnode version to the 1.1.9-1 in the Nym Wallet (Bonding - Node Settings section)

#

### 🟢 **GW UPDATING**
### Build nym-node
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
# git checkout master
git checkout nym-binaries-v2025.1-reeses
cargo build --release --bin nym-node
```

### Replace and restart
```bash
sudo systemctl stop nym-node && \
sudo mv ~/nym/target/release/nym-node $(which nym-node) && \
sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat
```

### Change version to 1.1.9-1 in the Nym Wallet
Menu Bonding -> Gateway Settings    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett.jpg)    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett2.jpg)    

########################################################################################################## ---- TO DELETE ---------------------------------- -->


#
#
#
____
____

## ( # install from scratch tl;dr )
just notes, general steps
```
# wallet 100 NYM
# apt update
# install dependencies
# ufw
# ulimit

# init (exit GW):
# nym-node run --id <NODE_ID> --mode exit-gateway --public-ips "$(curl -4 https://ifconfig.me)" --http-bind-address 0.0.0.0:8080 --mixnet-bind-address 0.0.0.0:1789 --location <LOCATION> --accept-operator-terms-and-conditions --wireguard-enabled true
### we didn't configure reverse proxy and wss, hence skipping '--hostname ""' flag
# description.toml (~/.nym/nym-nodes/<NODE_ID>/data/description.toml)

# service nym-node
# start nym-node
# bond [ nym-node bonding-information --id <NODE_ID> ]

# network_tunnel_manager.sh script
# reverse proxy & wss
# fill 'hostname =' in the config.toml
### restart
```


<!-- ######################################### Service #############
--------------- with wg enabled
```
sudo tee <<EOF >/dev/null /etc/systemd/system/nym-node.service
[Unit]
Description=Nym_node_exgw

[Service]
User=$USER
ExecStart=/usr/local/bin/nym-node run --id <NODE_ID> --mode exit-gateway --accept-operator-terms-and-conditions --wireguard-enabled true
KillSignal=SIGINT
Restart=on-failure
RestartSec=5
StartLimitInterval=350
StartLimitBurst=20
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


```
sudo tee <<EOF >/dev/null /etc/systemd/system/nym-node.service
[Unit]
Description=Nym-node-mixnode

[Service]
User=$USER
ExecStart=/usr/local/bin/nym-node run --id <NODE_ID> --mode mixnode --deny-init --public-ips <IPv4> --accept-operator-terms-and-conditions
KillSignal=SIGINT
Restart=on-failure
RestartSec=10
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

NG:
```
sudo tee <<EOF >/dev/null /etc/systemd/system/nym-node.service
[Unit]
Description=Nym Mixnode

[Service]
User=$USER
ExecStart=/usr/local/bin/nym-mixnode run --id '$node_id'
KillSignal=SIGINT
Restart=on-failure
RestartSec=30
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


######################################### Service ############# -->


<!-- ---------------------------- node move
  copy old server
nym-nodes dir, service file

update
ulimit
ufw

  change on new server (dir names if needed and config.toml)
node ID - if needed
node path - if the username has been changed
[example /home/<USERNAME>/.nym/nym-nodes/<NODE-ID>/data/x25519_noise.pub]
IP
Location - if changed

----------------------------- node move -->


<!-- ---------------------------- Download nym-node binary and run
cd ~/
# download binary
NYM_VERSION='nym-binaries-v2024.11-wedel'
wget https://github.com/nymtech/nym/releases/download/$NYM_VERSION/nym-node
chmod u+x nym-node
# stop nym-node
sudo systemctl stop nym-node
# backup current binary
cp $(which nym-node) nym-node-backup
# restart with new binary
sudo mv ~/nym-node $(which nym-node) && sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat -n 50

#
#

# Reverse #
# stop nym-node
sudo systemctl stop nym-node
# copy backup binary back
cp nym-node-backup $(which nym-node)
# restart with new binary
sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat -n 50
----------------------------- Download nym-node binary and run -->


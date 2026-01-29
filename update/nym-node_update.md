#### `quick update manual`

#

> The current design requires working with root or sudo    
> Nym operator docs https://nymtech.net/operators    
> #### These are just notes on how to update. Please refer to the official documentation to install nym node

# 29.01.2026 Nym Binaries v2026.2-oscypek `v1.24.0` `latest`   
Changelog: https://nymtech.net/docs/operators/changelog    
! IMPORTANT [To prevent *foreign constraint bug* we need to do a little sqlite tweak](https://nym.com/docs/operators/changelog#oscypek-special-update)

#

### UPDATING
#### BUILD or DOWNLOAD nym binarie

- #### BUILD
#### Build nym-node
```bash
cd
# rustup update
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v2026.2-oscypek
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
sudo mv ~/nym/target/release/nym-node /usr/local/bin/nym-node && \
sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat
```
- ### DOWNLOAD
> ! *The binary you downloaded may not match the architecture of your system*    
### Download nym-node binary
```sh
cd
rustup update
NYM_VERSION='nym-binaries-v2026.2-oscypek'
# download binary
wget https://github.com/nymtech/nym/releases/download/$NYM_VERSION/nym-node
chmod u+x nym-node
```

### Check downloaded binary info
```
./nym-node build-info
```

### Restart with the new version
```sh
cd
sudo systemctl stop nym-node &&\
sudo mv ~/nym-node /usr/local/bin/nym-node &&\
sudo systemctl restart nym-node &&\
sudo journalctl -u nym-node -f -o cat -n 50
```


<!-- ########################################################################################################## TO DELETE -----------------------------------

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


#


> ### Pre-installation
> ##### Update and install tools
> ```
> sudo apt update && sudo apt upgrade -y
> ```
> ```
> sudo dpkg --configure -a
> sudo apt install ufw git jq pkg-config libssl-dev build-essential ca-certificates
> ```
> 
> ### Install or update Rust
> ```
> sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
> source $HOME/.cargo/env
> rustup update
> ```
########################################################################################################## ---- TO DELETE end ---------------------------------- -->


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


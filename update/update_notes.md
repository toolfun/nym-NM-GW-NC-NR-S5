## 16.11.2022
### предвар ПЛАН:                                           
- #### Общее - пакеты, апдейт ...                      
- #### Стоп сервис, -> обновляем                       
- #### NM NR NC GW - гит клон и билд. мув бинарники    
- #### NM - инит, исправить конфиг где валидаторс апи  
- #### NR - исправил сервисник                         
- #### GW - перебонд на новую версию    
____

### ОБНОВЛЕНИЕ NYM MYXNODE 1.1.0 :
### Апдейт и установка инструментов
dpkg --configure -a
apt install ufw make clang pkg-config libssl-dev build-essential git сгкд

### Билд, см гайды по установке nym

### После установки новой версии:
#### Сделать init обновленной версии nym-mixnode, чтобы подтнуть нов версию (вроде можно просто исправить в конфиге, тут хз)
nym-mixnode init --id $node_id --host $(curl ifconfig.me) --wallet-address n16uc5zvupdflqp0f9txgtl4k7dq59dyjzwqdr7c
#### "api" Ошибка после обновления и при запуске обновленного nym v 1.1.0
#### Нужно добавить адрес апи в конфиг.томл
#### вручную в файле
.nym/mixnodes/nymiru/config/config.toml

```
## Addresses to APIs running on validator from which the node gets the view of the network.
validator_api_urls = [
        'https://validator.nymtech.net/api',    
]
```

#### сделать это командой
```
sed -i "s/validator_api_urls = \[/validator_api_urls = \['https:\/\/validator.nymtech.net\/api',/" $HOME/.nym/mixnodes/$node_id/config/config.toml
```

### ОБНОВЛЕНИЕ NYM CLIENT, NR, GW 1.1.0 :

Network Requester    
* pause your client and network requester processes    
* replace the binaries    
* restart the processes    

Gateway    
* pause your gateway process    
* replace the binary with the new binary    
* restart the process    

### (Инит не делал)
### (Менял в сервиснике NR строчку старта)

stop nym
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git reset --hard
git pull
git checkout nym-binaries-1.1.0
source $HOME/.bash_profile
cargo build -p nym-client --release && cargo build -p nym-network-requester --release

sudo mv target/release/nym-client /usr/local/bin/
sudo mv target/release/nym-network-requester /usr/local/bin/
```

> #### If the any of the processes are failing, then you might have to run init NR again: this will not overwrite keys or configs, so don't worry

### Новый сервисник NR без "--"
```
[Unit]
Description=Nym Network Requester

[Service]
User=root
ExecStart=/usr/local/bin/nym-network-requester run enable-statistics
KillSignal=SIGINT
Restart=on-failure
RestartSec=30
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

> #### *Сервисник Nym-client не менял*

### На всякий случай, config.toml of GW
```
validator_nymd_urls = [

        'https://rpc.nyx.nodes.guru/',

]
```

#=====================================================
#-----------------------------------------------------
### **Итого. инит делал только у миксноды, чтобы подтянулась в конфиг обновленная версия. В NC GW исправил руками в конфиг.томл**    
### *GW надо было перебондить на новую версию 1.1.0 (Стоп - анбонд - старт - бонд)*


###############################
### Проверка с пом socks5 клиента
Гит клон и поменять 127.0.0.0 на 0.0.0.0    
Билд    
Инит    
Ран    
Проверка на с5-клиент сервере:    
```
curl -x socks5h://localhost:1080 https://nymtech.net/.wellknown/connect/healthcheck.json
```

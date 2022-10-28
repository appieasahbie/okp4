# Okp4 Node Setup :


![download](https://user-images.githubusercontent.com/108979536/198153396-8bc7304c-a5d2-4b10-a9bb-13e52ba5afd0.jpg)

### OKP4 Protocol
OKP4 is a domain-specific layer-1 dedicated to trust-minimized data sharing.
The blockchain orchestrates assets shared by participants into the Dataverse: data, algorithms, software, storage and computation to enable a new generation of applications.
Any contributor earns rewards thanks to these new value chains.

# Official links :

  + Discord : [link](https://discord.gg/d6QfUzHR)

  + Website : [link](https://okp4.network/)

  + Twitter : [link](https://twitter.com/OKP4_Protocol)

  + Github : [link](https://github.com/okp4)



# Hardware Requirements

### Minimum Hardware Requirements

 + 4x CPUs; the faster clock speed the better
 
+  8GB RAM
 
+ 100GB of storage (SSD or NVME)

### Recommended Hardware Requirements

+ 8x CPUs; the faster clock speed the better

+ 64GB RAM
 
+  1TB of storage (SSD or NVME)


### 1-ɪɴᴛᴀʟʟᴀᴛɪᴏɴ ᴏɴᴇ ᴛɪᴍᴇ (ꜱᴄʀɪᴘᴛ ᴀᴜᴛᴏᴍᴀᴛɪᴄ ɪɴꜱᴛᴀʟʟᴀᴛɪᴏɴ)


      wget -O okp4.sh https://raw.githubusercontent.com/appieasahbie/okp4/main/okp4.sh && chmod +x okp4.sh && ./okp4.sh
      
###  Post installation

      source $HOME/.bash_profile
      
### (Check the status of your node)

      okp4d status 2>&1 | jq .SyncInfo
      
### open ports and active the firewall

      sudo ufw default allow outgoing
      sudo ufw default deny incoming
      sudo ufw allow ssh/tcp
      sudo ufw limit ssh/tcp
      sudo ufw allow ${OKP4_PORT}656,${OKP4_PORT}660/tcp
      sudo ufw enable
      
###  Create wallet

  + (Please save all keys on your notepad)

        okp4d keys add $WALLET
   
  + To recover your old wallet use this command
 
        okp4d keys add $WALLET --recover
        
### Add wallet and valoper address and load variables into the system

        OKP4_WALLET_ADDRESS=$(okp4d keys show $WALLET -a)
        OKP4_VALOPER_ADDRESS=$(okp4d keys show $WALLET --bech val -a)
        echo 'export OKP4_WALLET_ADDRESS='${OKP4_WALLET_ADDRESS} >> $HOME/.bash_profile
        echo 'export OKP4_VALOPER_ADDRESS='${OKP4_VALOPER_ADDRESS} >> $HOME/.bash_profile
        source $HOME/.bash_profile
        
   
### Fund your wallet (to create validator) [link faucet](https://faucet.okp4.network)


### Create validator (after recieving of tokens )

  + replace <wallet> with your wallet name and <moniker> with your validator name
  

         okp4d tx staking create-validator \
         --amount 100000uknow \
         --from WALLETNAME \
         --commission-max-change-rate "0.01" \
         --commission-max-rate "0.2" \
         --commission-rate "0.05" \
         --min-self-delegation "1" \
         --pubkey  $(okp4d tendermint show-validator) \
         --moniker NODENAME \
         --chain-id okp4-nemeton’
  
  
 # Snapchot optional (nodejumber)
 
   ### install dependencies, if needed
   
      sudo apt update
      sudo apt install lz4 -y
      
      sudo systemctl stop okp4d

      cp $HOME/.okp4d/data/priv_validator_state.json $HOME/.okp4d/priv_validator_state.json.backup
      okp4d tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book

      rm -rf $HOME/.okp4d/data 
      rm -rf $HOME/.okp4d/wasm

      SNAP_NAME=$(curl -s https://snapshots2-testnet.nodejumper.io/okp4-testnet/ | egrep -o ">okp4-nemeton.*\.tar.lz4" | tr -d ">")
      curl https://snapshots2-testnet.nodejumper.io/okp4-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.okp4d

      mv $HOME/.okp4d/priv_validator_state.json.backup $HOME/.okp4d/data/priv_validator_state.json

     sudo systemctl restart okp4d
     sudo journalctl -u okp4d -f --no-hostname -o cat



### Delegate to yourself

      okp4d tx staking delegate $(okp4d keys show wallet --bech val -a) 1000000uknow --from wallet --chain-id okp4-nemeton --gas-prices 0.1uknow --gas-adjustment 1.5 --gas auto -y 
      
 
 ### Redelegate
 
      okp4d tx staking redelegate $(okp4d keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uknow --from wallet --chain-id okp4-nemeton --gas-prices 0.1uknow --gas-adjustment 1.5 --gas auto -y 
     
 ### Get Validator Info

     okp4d status 2>&1 | jq .ValidatorInfo

### Get Catching Up

     okp4d status 2>&1 | jq .SyncInfo.catching_up
 
### Get Latest Height

     okp4d status 2>&1 | jq .SyncInfo.latest_block_height

## Get Peer

     echo $(okp4d tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.okp4d/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')

### Reset Node

    okp4d tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book

### Remove Node

    sudo systemctl stop okp4d && sudo systemctl disable okp4d && sudo rm /etc/systemd/system/okp4d.servi
     
###  Run Service

    sudo systemctl start okp4d

### Stop Service

    sudo systemctl stop okp4d

### Restart Service

    sudo systemctl restart okp4d

### Check Service Status

    sudo systemctl status okp4d

### Check Service Logs

   sudo journalctl -u okp4d -f --no-hostname -o cat     

### Delete the node 

    sudo systemctl stop okp4d
    sudo systemctl disable okp4d
    sudo rm /etc/systemd/system/okp4* -rf
    sudo rm $(which okp4d) -rf
    sudo rm $HOME/.okp4d* -rf
    sudo rm $HOME/okp4 -rf
    sed -i '/OKP4_/d' ~/.bash_profile


    
[buy me a cup of coffe ](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL&locale.x=en_US)   
     

﻿\# Migrate your validator to another machine

\### 1. Run a new full node on a new machine

To setup full node you can follow my guide [sei node setup for testnet](https://github.com/kj89/testnet\_manuals/blob/main/sei/README.md)

\### 2. Confirm that you have the recovery seed phrase information for the active key running on the old machine

\#### To backup your key

\```

seid keys export mykey

\```

\> \_This prints the private key that you can then paste into the file `mykey.backup`\_

\#### To get list of keys

\```

seid keys list

\```

\### 3. Recover the active key of the old machine on the new machine

\#### This can be done with the mnemonics

\```

seid keys add mykey --recover

\```

\#### Or with the backup file `mykey.backup` from the previous step

\```

seid keys import mykey mykey.backup

\```

\### 4. Wait for the new full node on the new machine to finish catching-up

\#### To check synchronization status

\```

seid status 2>&1 | jq .SyncInfo

\```

\> \_`catching\_up` should be equal to `false`\_

\### 5. After the new node has caught-up, stop the validator node

\> \_To prevent double signing, you should stop the validator node before stopping the new full node to ensure the new node is at a greater block height than the validator node\_

\> \_If the new node is behind the old validator node, then you may double-sign blocks\_

\#### Stop and disable service on old machine

\```

sudo systemctl stop seid

sudo systemctl disable seid

\```

\> \_The validator should start missing blocks at this point\_

\### 6. Stop service on new machine

\```

sudo systemctl stop seid

\```

\### 7. Move the validator's private key from the old machine to the new machine

\#### Private key is located in: `~/.seid/config/priv\_validator\_key.json`

\> \_After being copied, the key `priv\_validator\_key.json` should then be removed from the old node's config directory to prevent double-signing if the node were to start back up\_

\```

sudo mv ~/.seid/config/priv\_validator\_key.json ~/.seid/bak\_priv\_validator\_key.json

\```

\### 8. Start service on a new validator node

\```

sudo systemctl start seid

\```

\> \_The new node should start signing blocks once caught-up\_

\### 9. Make sure your validator is not jailed

\#### To unjail your validator

\```

seid tx slashing unjail --chain-id $SEI\_CHAIN\_ID --from mykey --gas=auto -y

\```

![Allora x BlockFarms](./logos.png)

# Validator Commands
Chain Id: 
```
edgenet
```



### Daemon Commands
How to create a validator
```
cat > /your/directory/path/allora/config/stake-validator.json << EOF
{
    "pubkey": $(allorad --home /your/directory/path/allora/ tendermint show-validator),
    "amount": "1000000uallo",
    "moniker": "My Validator",
    "website": "My-website.com",
    "identity": "keybaseID",
    "details": "My validator details",
    "security": "email@gmail.com",
    "commission-rate": "0.10",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
EOF

allorad tx staking create-validator /your/directory/path/allora/config/stake-validator.json \
    --chain-id=edgenet \
    --home="/your/directory/path/allora/" \
    --keyring-backend=test \
    --from="your-wallet-name" \
    -y
```

How to edit validator details 
```
allorad tx staking edit-validator /your/directory/path/allora/config/stake-validator.json \
    --chain-id=edgenet \
    --home="/your/directory/path/allora/" \
    --keyring-backend=test \
    --from="your-wallet-name" \
    -y
```

How to increase validator self ALLO stake (delegation)
```
allorad tx staking delegate \
    yourValidatorValoperAddress \
    1000000uallo \
    --from yourValidatorAddress \
    --gas="auto" \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000allo
    --account-number=yourAccountNumber \
    --chain-id=edgenet
```

How to withdraw all your validator rewards (self delegation & commission)
```
allorad tx distribution withdraw-all-rewards \
    --from yourValidatorAddress \
    --chain-id=edgenet \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000allo
```

How to send ALLO to another address
```
allorad tx bank send \
    fromAddress \
    toAddress \
    10000000000000000000000allo \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000allo \
    -y
```


How to unjail your validator
```
allorad tx slashing unjail \
    --from yourValidatorAddress \
    --chain-id=edgenet \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000allo \
    -y
```

How to unbond 10,000 MPX or stop (if that is all the tokens) your validator
```
allorad tx staking unbond \
    yourValidatorValoperAddress \
    10000000000000000000000allo \
    --from yourValidatorAddress \
    --chain-id=edgenet \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000allo \
    -y
```

### Wallet & Balance Commands
List all wallets 
```
allorad keys list
```

Import existing wallet
```
allorad keys import yourWallet yourWallet.txt
```

Export existing wallet
```
allorad keys export yourWallet
```

Create new wallet
```
allorad keys add yourWallet
```

Recovery previous wallet with seed phrase
```
allorad keys add validator --recover
```

Delete wallet
```
allorad keys delete yourWallet
```

Check token balances
```
allorad q bank balances $(allorad keys show yourWallet -a)
```

### Governance

View Proposals
```
allorad query gov proposals
```

Vote on Proposals
```
allorad tx gov vote \
    proposalNumber \
    yes/no \
    --from yourValidatorAddress \
    --chain-id=edgenet \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000allo \
    -y
```


### Basic DevOps & Monitoring

Check Logs
```
journalctl -u allorad -f
```

Check syncing status (True/False), get latest block, and various validator info (Peer Address)
```
curl -s localhost:26657/status
```

Check validator status
```
systemctl status allorad
```

Start validator
```
systemctl start allorad
```

Reload daemon of the validator
```
systemctl daemon-reload
```

Stop Validator
```
systemctl stop allorad
```

Restart Validator
```
systemctl restart allorad
```

Enable Validator start upon machine startup
```
systemctl enable allorad
```

Disable Validator start upon machine startup
```
systemctl disable allorad
```


Check Prometheus metrics via Curl
```
curl localhost:26660/metrics
```


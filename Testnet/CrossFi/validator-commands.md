![CrossFi x BlockFarms](./logos.png)

# Validator Commands
Chain Id: 
```
crossfi-evm-testnet-1
```



### Daemon Commands
How to create a validator with 10,000 MPX tokens. [reference](https://docs.crossfi.org/crossfi-chain/technical-information/validators)
```
crossfid tx staking create-validator \
  --amount=10000000000000000000000mpx \
  --pubkey=$(crossfid --home /your/directory/path/crossfi/ tendermint show-validator) \
  --moniker="My Validator" \
  --website="My-website.com" \
  --identity=keybaseID \
  --details="My validator details" \
  --security-contact="email@gmail.com" \
  --chain-id=crossfi-evm-testnet-1 \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --gas="auto" \
  --gas-prices="10000000000000mpx" \
  --gas-adjustment=1.5 \
  --from=validator
```

How to edit validator details [reference](https://docs.crossfi.org/crossfi-chain/technical-information/validators)
```
crossfid tx staking edit-validator \
  --new-moniker="My Validator" \
  --website="My-website.com" \
  --identity=keybaseID \
  --details="My validator details" \
  --security-contact="email@gmail.com" \
  --chain-id=crossfi-evm-testnet-1 \
  --gas="auto" \
  --gas-prices="10000000000000mpx" \
  --gas-adjustment=1.5 \
  --from=validator \
  --commission-rate="0.050"
```

How to increase validator self MPX stake (delegation) by 10,000 MPX
```
crossfid tx staking delegate \
    yourValidatorValoperAddress \
    10000000000000000000000mpx \
    --from yourValidatorAddress \
    --gas="auto" \
    --gas-prices="10000000000000mpx" \
    --gas-adjustment=1.5 \
    --account-number=yourAccountNumber \
    --chain-id=crossfi-evm-testnet-1
```

How to withdraw all your validator rewards (self delegation & commission)
```
crossfid tx distribution withdraw-all-rewards \
    --from yourValidatorAddress \
    --chain-id crossfi-evm-testnet-1 \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000mpx
```

How to send 10,000 MPX to another address
```
crossfid tx bank send \
    fromAddress \
    toAddress \
    10000000000000000000000mpx \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000mpx \
    -y
```


How to unjail your validator
```
crossfid tx slashing unjail \
    --from yourValidatorAddress \
    --chain-id crossfi-evm-testnet-1 \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000mpx \
    -y
```

How to unbond 10,000 MPX or stop (if that is all the tokens) your validator
```
crossfid tx staking unbond \
    yourValidatorValoperAddress \
    10000000000000000000000mpx \
    --from yourValidatorAddress \
    --chain-id crossfi-evm-testnet-1 \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000mpx \
    -y
```

### Wallet & Balance Commands
List all wallets 
```
crossfid keys list
```

Import existing wallet
```
crossfid keys import yourWallet yourWallet.txt
```

Export existing wallet
```
crossfid keys export yourWallet
```

Create new wallet
```
crossfid keys add yourWallet
```

Delete wallet
```
crossfid keys delete yourWallet
```

Check token balances
```
crossfid q bank balances $(crossfid keys show yourWallet -a)
```

### Governance

View Proposals
```
crossfid query gov proposals
```

Vote on Proposals
```
crossfid tx gov vote \
    proposalNumber \
    yes/no \
    --from yourValidatorAddress \
    --chain-id crossfi-evm-testnet-1 \
    --gas auto \
    --gas-adjustment 1.5 \
    --gas-prices 10000000000000mpx \
    -y
```


### Basic DevOps & Monitoring

Check Logs
```
journalctl -u crossfid -f
```

Check syncing status (True/False), get latest block, and various validator info (Peer Address)
```
curl -s localhost:26657/status
```

Check validator status
```
systemctl status crossfid
```

Start validator
```
systemctl start crossfid
```

Reload daemon of the validator
```
systemctl daemon-reload
```

Stop Validator
```
systemctl stop crossfid
```

Restart Validator
```
systemctl restart crossfid
```

Enable Validator start upon machine startup
```
systemctl enable crossfid
```

Disable Validator start upon machine startup
```
systemctl disable crossfid
```


Check Prometheus metrics via Curl
```
curl localhost:26660/metrics
```


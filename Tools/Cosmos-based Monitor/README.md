# Cosmos Validator Watcher
Cosmos Validator Watcher is a Prometheus exporter to help you monitor missed blocks on any cosmos-based blockchains in real-time.

Features:
- Track when your validator missed a block (with solo option)
- Check how many validators missed the signatures for each block
- Track the current active set and check if your validator is bonded or jailed
- Track the staked amount as well as the min seat price
- Track pending proposals and check if your validator has voted (including proposal end time)
- Expose upgrade plan to know when the next upgrade will happen (including pending proposals)
- Trigger webhook when an upgrade happens

https://github.com/kilnfi/cosmos-validator-watcher?tab=readme-ov-file

## Install validator prometheus exporter
Configure firewall ports
```
ufw allow from 127.0.0.1 to any port 7070 proto tcp && # validator prometheus exporter
```

Get validator pub address in hex format
```
mineplex-chaind debug pubkey "$(mineplex-chaind --home ~/mineplex tendermint show-validator)"
```
> 0D69513F99994B3374C01A69F2FB511EDB6F9D58

Run the Validator Watcher docker container
```
docker run -d \
  --name validator-watcher \
  --network host \
  --restart unless-stopped \
  --log-driver json-file \
  --log-opt max-size=100m \
  --log-opt max-file=1 \
  --log-opt tag="{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}" \
  ghcr.io/kilnfi/cosmos-validator-watcher:latest \
  --chain-id mainnet \
  --http-addr :7070 \
  --log-level info \
  --namespace validator_watcher \
  --node http://localhost:26657 \
  --validator 0D69513F99994B3374C01A69F2FB511EDB6F9D58:Validator-name
```

Remove the container
```
docker rm -f validator-watcher
```

Read the container logs
```
docker logs --tail 100 validator-watcher
```

Confirm Validator Watcher's Prometheus metrics
```
curl localhost:7070/metrics
curl localhost:7070/ready
curl localhost:7070/live
```

## References
https://github.com/kilnfi/cosmos-validator-watcher?tab=readme-ov-file
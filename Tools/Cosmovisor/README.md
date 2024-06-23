# Cosmovisor
Cosmovisor is a small process manager for Cosmos SDK application binaries that monitors the governance module for incoming chain upgrade proposals. If Cosmovisor sees a proposal that gets approved, cosmovisor can automatically download the new binary, stop the current binary, switch from the old binary to the new one, and finally restart the node with the new binary.

Node operators should prioritize utilizing Cosmovisor, as the tool will maximize uptime, and therefore reduce probabilities of slashing upon binary upgrades for the given network.

## Installing Cosmovisor on Ubuntu 22.04
### Install Prerequisites: Go
The Go version you may need based upon the latest Cosmovisor and your specific validator binary may differ than this guide. Finding a Go version that works for both is the minimum operating requirement

#### Installing Go v1.20.6
Remove any versions of Go already installed
```
rm -rf /usr/local/go
sed -i '/export PATH=\$PATH:\/usr\/local\/go\/bin/d' ~/.profile
sed -i '/export GOPATH=\$HOME\/go/d' ~/.profile
source ~/.profile
rm -rf $HOME/go
```

Confirm Go is properly uninstalled
```
go version
```
> Command 'go' not found, but can be installed with:...

Install Go v1.20.6
```
curl https://raw.githubusercontent.com/canha/golang-tools-install-script/master/goinstall.sh | bash
cat <<'EOF' >>~/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source ~/.profile
```

Confirm Go is properly installed
```
go version
```
> go version go1.20.6 linux/amd64

#### Installing other Go versions
https://go.dev/dl/

### Install Cosmovisor
Install Cosmovisor with Go v1.20.6
```
mkdir -p $GOPATH/src/gopkg.in/yaml.v2
git clone https://github.com/go-yaml/yaml.git $GOPATH/src/gopkg.in/yaml.v2
mkdir -p $GOPATH/src/gopkg.in/yaml.v3
git clone https://github.com/go-yaml/yaml.git $GOPATH/src/gopkg.in/yaml.v3
mkdir -p $GOPATH/src/gopkg.in/ini.v1
git clone https://github.com/go-ini/ini.git $GOPATH/src/gopkg.in/ini.v1
export GOPROXY=https://proxy.golang.org,direct
go clean -modcache
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

Confirm Cosmovisor is installed properly
```
cosmovisor --help
```

Setup directories for the given network
```
mkdir ~/.example-network/cosmovisor
mkdir ~/.example-network/cosmovisor/genesis
mkdir ~/.example-network/cosmovisor/genesis/bin
mkdir ~/.example-network/cosmovisor/upgrades
mkdir ~/.example-network/cosmovisor/data-backups
chmod -R 755 ~/.example-network/cosmovisor
```

Ensure directories are setup properly
```
cosmovisor init ~/.example-network/cosmovisor
```

Directory layout should look like this:
```
.
├── current -> genesis or upgrades/<name>
├── genesis
│   └── bin
│       └── $DAEMON_NAME
└── upgrades
│   └── <name>
│       ├── bin
│       │   └── $DAEMON_NAME
│       └── upgrade-info.json
└── preupgrade.sh (optional)
```

#### Copy binaries
In this example the network node daemon/binary is called `exampled`
```
cp ~/genesis-exampled ~/.example-network/cosmovisor/genesis/bin/exampled
cp ~/current-exampled ~/.example-network/cosmovisor/current/bin/exampled
```

Ensure versions are the same of Cosmovisor and the network daemon
Both outputs should be the same
```
cosmovisor run version
exampled version
```

#### Update environment paths
This will always ensure that the operator interacting with the CLI daemon will always interact with the current daemon regardless of upgraded binaries, etc. as Cosmovisor moves the current binary to the `current/bin` directory.
```
echo "# Setup Cosmovisor" >> ~/.profile
echo "export DAEMON_NAME=exampled" >> ~/.profile
echo "export DAEMON_HOME=~/.example-network" >> ~/.profile
source ~/.profile

export PATH=$PATH:~/.example-network/cosmovisor/current/bin/exampled
echo 'export PATH=$PATH:~/.example-network/cosmovisor/current/bin/exampled' >> ~/.bashrc
source ~/.bashrc
```

## Integrating Cosmovisor with your network validator binary
Creating a systemctl process for the validator utilizing Cosmovisor
```
tee /etc/systemd/system/exampled.service > /dev/null <<EOF
[Unit]
Description=Example Validator Daemon
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=~/.example-network
ExecStart=$(which cosmovisor) run start --home ~/.example-network
StandardOutput=append:~/.example-network/logs/exampled.log
StandardError=append:~/.example-network/logs/exampled.log
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

Environment="DAEMON_NAME=exampled"
Environment="DAEMON_HOME=~/.example-network"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=false"
Environment="DAEMON_DATA_BACKUP_DIR=~/.example-network/cosmovisor/data-backups"

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable exampled
systemctl start exampled
# systemctl restart exampled
# systemctl stop exampled
systemctl status exampled
```

The option `DAEMON_ALLOW_DOWNLOAD_BINARIES=false` does not make Cosmovisor automatically download new binaries when detected. This is to avoid potential issues with the soon-to-be upgraded binary, as Cosmovisor does not verify in advance if the binary is available. If there will be any issue with downloading a binary, Cosmovisor will stop and won't restart and the chain (which could lead it to a halt).

Therefore it is perferable for operators to manually download the binaries prior to chain upgrades, to confirm the correct network Daemon. This is common best practice devOps practice in prod, to never auto update anything anyway.


#### Read the validator Daemon's log file
```
cat ~/.example-network/logs/exampled.log
```

## Using Cosmovisor to automate network validator binary @ block height
[Usage](https://docs.cosmos.network/main/build/tooling/cosmovisor#adding-upgrade-binary)
```
cosmovisor add-upgrade [upgrade-name] [path to executable] --upgrade-height [int]
```

In practice, upgrading from v1.0.0 to v1.1.0 @ block height 1,000
```
wget -O ~/.example-network/cosmovisor/upgrades/exampled https://download-binary-file-url
chmod +x ~/.example-network/cosmovisor/upgrades/exampled
cosmovisor add-upgrade v1.1.0 ~/.example-network/cosmovisor/upgrades/exampled --upgrade-height 1000
```


## References
https://docs.cosmos.network/main/build/tooling/cosmovisor#installation

https://github.com/cosmos/cosmos-sdk/releases/tag/cosmovisor%2Fv1.5.0

https://artifact-staking.medium.com/running-evmos-testnet-with-cosmovisor-222701c70ce7

https://go.dev/doc/install

https://go.dev/dl/
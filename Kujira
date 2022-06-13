#!/bin/bash
exists()
{
  command -v "$1" >/dev/null 2>&1
}
if exists curl; then
echo ''
else
  sudo apt update && sudo apt install curl -y < "/dev/null"
fi
bash_profile=$HOME/.bash_profile
if [ -f "$bash_profile" ]; then
    . $HOME/.bash_profile
fi
sleep 1 && curl -s https://api.nodes.guru/logo.sh | bash && sleep 1


if [ ! $KUJIRAD_NODENAME ]; then
read -p "Enter node name: " KUJIRAD_NODENAME
echo 'export KUJIRAD_NODENAME='\"${KUJIRAD_NODENAME}\" >> $HOME/.bash_profile
fi
echo 'source $HOME/.bashrc' >> $HOME/.bash_profile
. $HOME/.bash_profile
sleep 1
cd $HOME
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"

echo -e '\n\e[42mInstall Go\e[0m\n' && sleep 1
cd $HOME
wget -O go1.18.1.linux-amd64.tar.gz https://golang.org/dl/go1.18.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz && rm go1.18.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version

echo -e '\n\e[42mInstall software\e[0m\n' && sleep 1
rm -rf $HOME/kujira-core
git clone https://github.com/Team-Kujira/core $HOME/kujira-core
cd $HOME/kujira-core
make install
sleep 1
ln -s $HOME/go/bin/kujirad /usr/local/bin/kujirad
kujirad init "$KUJIRAD_NODENAME" --chain-id=harpoon-4

#seeds="8e1590558d8fede2f8c9405b7ef550ff455ce842@51.79.30.9:26656,bfffaf3b2c38292bd0aa2a3efe59f210f49b5793@51.91.208.71:26656,106c6974096ca8224f20a85396155979dbd2fb09@198.244.141.176:26656"
#peers="peers="f559d66add4e0821b1996b04f8e73a948d0757bd@161.97.74.88:46656,aa3643753c57f85148906a2f44fb85d3d583fe9a@49.12.224.69:26656,57e09261178996f024ba337a0bdce64dccb73901@194.163.162.31:26641,3f26aa959f2a0f16749747c63767c1e491275cae@88.198.242.163:26656,ca7d24539da9f2dbd84e7f2991608910d1843d2d@65.21.134.202:26616,87ea1a43e7eecdd54399551b767599921e170399@52.215.221.93:26656,021b782ba721e799cd3d5a940fc4bdad4264b148@65.108.103.236:16656,1d6f841271a1a3f78c6772b480523f3bb09b0b0b@15.235.47.99:26656,ccd2861990a98dc6b3787451485b2213dd3805fa@185.144.99.234:26656,909b8da1ea042a75e0e5c10dc55f37711d640388@95.216.208.150:53756,235d6ac8aebf5b6d1e6d46747958c6c6ff394e49@95.111.245.104:26656,b525548dd8bb95d93903b3635f5d119523b3045a@194.163.142.29:26656,26876aff0abd62e0ab14724b3984af6661a78293@139.59.38.171:36347,21fb5e54874ea84a9769ac61d29c4ff1d380f8ec@188.132.128.149:25656,06ebd0b308950d5b5a0e0d81096befe5ba07e0b3@193.31.118.143:25656,f9ee35cf9aec3010f26b02e5b3354efaf1c02d53@116.203.135.192:26656,c014d76c1a0d1e0d60c7a701a7eff5d639c6237c@157.90.179.182:29656,0ae4b755e3da85c7e3d35ce31c9338cb648bba61@164.92.187.133:26656,202a3d8bd5a0e151ced025fc9cbff606845c6435@49.12.222.155:26656"
#sed -i "s/^seeds *=.*/seeds = \"$seeds\"/;" $HOME/.kujira/config/config.toml
#sed -i "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/;" $HOME/.kujira/config/config.toml

#### VALIDATOR ####
sed -i "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.kujira/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"809\"/g" $HOME/.kujira/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"43\"/g" $HOME/.kujira/config/app.toml
#sed -i.bak -e "s/indexer *=.*/indexer = \"null\"/g" $HOME/.kujira/config/config.toml
sed -i "s/index-events =.*/index-events = [\"tx.hash\",\"tx.height\"]/g" $HOME/.kujira/config/app.toml
wget -O $HOME/.kujira/config/genesis.json "https://raw.githubusercontent.com/Team-Kujira/networks/master/testnet/harpoon-4.json
kujirad tendermint unsafe-reset-all
wget -O $HOME/.kujira/config/addrbook.json https://raw.githubusercontent.com/Team-Kujira/networks/master/testnet/addrbook.json 
echo -e '\n\e[42mRunning\e[0m\n' && sleep 1
echo -e '\n\e[42mCreating a service\e[0m\n' && sleep 1

echo "[Unit]
Description=Kujirad Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which kujirad) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/kujirad.service
sudo mv $HOME/kujirad.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
echo -e '\n\e[42mRunning a service\e[0m\n' && sleep 1
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable kujirad
sudo systemctl restart kujirad

echo -e '\n\e[42mCheck node status\e[0m\n' && sleep 1
if [[ `service kujirad status | grep active` =~ "running" ]]; then
  echo -e "Your kujira node \e[32minstalled and works\e[39m!"
  echo -e "You can check node status by the command \e[7mservice kujirad status\e[0m"
  echo -e "Press \e[7mQ\e[0m for exit from status menu"
else
  echo -e "Your kujirad node \e[31mwas not installed correctly\e[39m, please reinstall."
fi

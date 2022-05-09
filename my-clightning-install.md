# C-Lightning build and install steps 

## first I did the usually stuff, cloned from github etc...
sudo apt-get update
sudo apt-get install -y \
  autoconf automake build-essential git libtool libgmp-dev libsqlite3-dev \
  python3 python3-mako python3-pip net-tools zlib1g-dev libsodium-dev \
  gettext
pip3 install --user mrkd mistune==0.8.4
sudo apt-get install snapd <!-- not sure if you need this, but installing it just in case --> 
## I already have bitcoind installed and running, so I plan to use the existing daemon
./configure
make

sudo pip3 install mrkd <!-- had to install this after getting an error *mrkd: not found* -->

https://medium.com/coinmonks/how-to-set-up-a-lightning-node-connect-to-a-node-and-open-a-channel-4-4-efd627d7ff32
wget https://dl.google.com/go/go1.17.1.linux-armv6l.tar.gz
sha256sum go1.17.1.linux-armv6l.tar.gz | awk -F " " '{ print $1 }'
# make sure its ed3e4dbc9b80353f6482c441d65b51808290e94ff1d15d56da5f4a7be7353758
sudo tar -C /usr/local -xzf go1.17.1.linux-armv6l.tar.gz
export PATH=$PATH:/usr/local/go/bin
export GOPATH=~/go
export PATH=$PATH:$GOPATH/bin

# A new way that worked:

sudo apt update
sudo apt -y install golang
GOPATH=$HOME/go
PATH=$PATH:$GOPATH/bin <!-- would be best to put this in the .bashrc as part of the PATH on startup, havent done that yet so I have to re-type this every session -->
sudo apt-get install -y build-essential <!-- I already had build-tools installed from setting up my env for packaging -->

git clone https://github.com/lightningnetwork/lnd
cd lnd
git checkout [latest-version] 
make && make install tags="autopilotrpc chainrpc invoicesrpc routerrpc signrpc walletrpc watchtowerrpc wtclientrpc"

# Create a configuration file for LND

mkdir ~/.lnd
touch ~/.lnd/lnd.conf
sudo nano ~/.lnd/lnd.conf <!-- You can copy and paste from the sample lnd file on the repo, and then change what you want from there. -->
## Its important to set up the bitcoin backend to match your .bitcoin conf file, including the rpc username and password.

# Starting LND
## Took a long time to get ZMQ to work, I didnt realize I needed it until I tried to run LND and got a connection error at that step.
## you can run bitcoind and lnd in the background by including -daemon on the command line, or setting daemon=1 in the conf file respectively. I kept open bash windows so i could watch the logs and see whats happenning

lnd <!-- This will start up LND, but it will ask you to create a wallet or unlock an existing one -->
## Guides say to edit crontab (crontab -e) so that LND runs on startup, I dont know how to do that yet
## Create a new cipher seed, and then LND should start up automatically. (lnd successfully initialized!)

lncli create <!-- this creates a new wallet, follow the prompts to create a password -->

## I needed to restart a few tims to fix things, and had to unlock the wallet, so use this command if you need to do that
lncli unlock <!-- enter password when prompted -->
lncli newaddress p2wkh <!-- creates a new address to be used with your LND node my example is "address": "bc1qzxx9ynf6hc0zg0efyceq7nfr3mlcec5tnr9qp7"-->
# **Dont send bitcoin to this address, I will take it!** 

<!--  -->
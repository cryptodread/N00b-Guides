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

## Tor Setup for LND
https://wiki.ion.radar.tech/tutorials/nodes/tor

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
Sent myself 100,000 sats 

lncli getinfo <!-- to get my node pubkey, but I still dont know how to get the socket and port -->
lncli openchannel --conf_target 3 --node_key 03a6daac0ca23f681f86f7a3dba5674ae5eaa5c171571ab1ece5bf10c0ad7b4c4e --connect 3pwq2jhhktvyv5fbkvghhm5gmqgf7yfhbb6vd6tpasjmm3lidt7mnsad.onion:9735 --local_amt 50000

lncli openchannel --conf_target 6 --node_key 0377c1b95f169934e3e8d1d376deb963550a20797f2777f90ee90e7a3c6ddcde30 --connect 99.151.48.66:9735 --local_amt 50000

<!--  -->

NAME:
   lncli - control plane for your Lightning Network Daemon (lnd)

USAGE:
   lncli [global options] command [command options] [arguments...]

VERSION:
   0.14.2-beta commit=v0.14.2-beta

COMMANDS:
     getinfo          Returns basic information related to the active daemon.
     getrecoveryinfo  Display information about an ongoing recovery attempt.
     debuglevel       Set the debug level.
     stop             Stop and shutdown the daemon.
     version          Display lncli and lnd version info.
     sendcustom       
     subscribecustom  
     help, h          Shows a list of commands or help for one command
   Autopilot:
     autopilot  Interact with a running autopilot.
   Channels:
     openchannel        Open a channel to a node or an existing peer.
     batchopenchannel   Open multiple channels to existing peers in a single transaction.
     closechannel       Close an existing channel.
     closeallchannels   Close all existing channels.
     abandonchannel     Abandons an existing channel.
     channelbalance     Returns the sum of the total available channel balance across all open channels.
     pendingchannels    Display information pertaining to pending channels.
     listchannels       List all open channels.
     closedchannels     List all closed channels.
     getnetworkinfo     Get statistical information about the current state of the network.
     feereport          Display the current fee policies of all active channels.
     updatechanpolicy   Update the channel policy for all channels, or a single channel.
     exportchanbackup   Obtain a static channel back up for a selected channels, or all known channels.
     verifychanbackup   Verify an existing channel backup.
     restorechanbackup  Restore an existing single or multi-channel static channel backup.
     updatechanstatus   Set the status of an existing channel on the network.
   Graph:
     describegraph   Describe the network graph.
     getnodemetrics  Get node metrics.
     getchaninfo     Get the state of a channel.
     getnodeinfo     Get information on a specific node.
   Invoices:
     addinvoice      Add a new invoice.
     lookupinvoice   Lookup an existing invoice by its payment hash.
     listinvoices    List all invoices currently stored within the database. Any active debug invoices are ignored.
     decodepayreq    Decode a payment request.
     cancelinvoice   Cancels a (hold) invoice.
     addholdinvoice  Add a new hold invoice.
     settleinvoice   Reveal a preimage and use it to settle the corresponding invoice.
   Macaroons:
     bakemacaroon      Bakes a new macaroon with the provided list of permissions and restrictions.
     listmacaroonids   List all macaroons root key IDs in use.
     deletemacaroonid  Delete a specific macaroon ID.
     listpermissions   Lists all RPC method URIs and the macaroon permissions they require to be invoked.
     printmacaroon     Print the content of a macaroon in a human readable format.
   Mission Control:
     querymc    Query the internal mission control state.
     queryprob  Estimate a success probability.
     resetmc    Reset internal mission control state.
     getmccfg   Display mission control's config.
     setmccfg   Set mission control's config.
   On-chain:
     estimatefee    Get fee estimates for sending bitcoin on-chain to multiple addresses.
     sendmany       Send bitcoin on-chain to multiple addresses.
     sendcoins      Send bitcoin on-chain to an address.
     listunspent    List utxos available for spending.
     listchaintxns  List transactions from the wallet.
   Payments:
     sendpayment     Send a payment over lightning.
     payinvoice      Pay an invoice over lightning.
     sendtoroute     Send a payment over a predefined route.
     listpayments    List all outgoing payments.
     queryroutes     Query a route to a destination.
     fwdinghistory   Query the history of all forwarded HTLCs.
     trackpayment    Track progress of an existing payment.
     deletepayments  Delete a single or multiple payments from the database.
     importmc        Import a result to the internal mission control state.
     buildroute      Build a route from a list of hop pubkeys.
   Peers:
     connect     Connect to a remote lnd peer.
     disconnect  Disconnect a remote lnd peer identified by public key.
     listpeers   List all active, currently connected peers.
   Profiles:
     profile  Create and manage lncli profiles.
   Startup:
     create           Initialize a wallet when starting lnd for the first time.
     createwatchonly  Initialize a watch-only wallet after starting lnd for the first time.
     unlock           Unlock an encrypted wallet at startup.
     changepassword   Change an encrypted wallet's password at startup.
     state            Get the current state of the wallet and RPC
   Wallet:
     newaddress     Generates a new address.
     walletbalance  Compute and display the wallet's current balance.
     signmessage    Sign a message with the node's private key.
     verifymessage  Verify a message signed with the signature.
     wallet         Interact with the wallet.
   Watchtower:
     tower     Interact with the watchtower.
     wtclient  Interact with the watchtower client.

GLOBAL OPTIONS:
   --rpcserver value          The host:port of LN daemon. (default: "localhost:10009")
   --lnddir value             The path to lnd's base directory. (default: "/home/dread/.lnd")
   --tlscertpath value        The path to lnd's TLS certificate. (default: "/home/dread/.lnd/tls.cert")
   --chain value, -c value    The chain lnd is running on, e.g. bitcoin. (default: "bitcoin")
   --network value, -n value  The network lnd is running on, e.g. mainnet, testnet, etc. (default: "mainnet")
   --no-macaroons             Disable macaroon authentication.
   --macaroonpath value       The path to macaroon file.
   --macaroontimeout value    Anti-replay macaroon validity time in seconds. (default: 60)
   --macaroonip value         If set, lock macaroon to specific IP address.
   --profile value, -p value  Instead of reading settings from command line parameters or using the default profile, use a specific profile. If a default profile is set, this flag can be set to an empty string to disable reading values from the profiles file.
   --macfromjar value         Use this macaroon from the profile's macaroon jar instead of the default one. Can only be used if profiles are defined.
   --help, -h                 show help
   --version, -v              print the version
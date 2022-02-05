wget https://dl.google.com/go/go1.17.1.linux-armv6l.tar.gz
sha256sum go1.17.1.linux-armv6l.tar.gz | awk -F " " '{ print $1 }'
# make sure its ed3e4dbc9b80353f6482c441d65b51808290e94ff1d15d56da5f4a7be7353758
sudo tar -C /usr/local -xzf go1.17.1.linux-armv6l.tar.gz
export PATH=$PATH:/usr/local/go/bin
export GOPATH=~/go
export PATH=$PATH:$GOPATH/bin


New way that worked:

sudo apt update
sudo apt -y install golang
git clone https://github.com/lightningnetwork/lnd
cd lnd
make install

<!--  -->
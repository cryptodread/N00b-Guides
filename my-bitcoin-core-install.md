Build Bitcoin Core:

git clone https://github.com/bitcoin/bitcoin.git
cd bitcoin
sudo apt git install build-essential libtool autotools-dev automake pkg-config bsdmainutils python3
sudo apt install libdb-dev libdb++-dev
sudo apt install libsqlite3-dev
sudo apt install libminiupnpc-dev libnatpmp-dev
sudo apt-get install libzmq3-dev
sudo apt install systemtap-sdt-dev
sudo apt-get install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools
sudo apt install qtwayland5
sudo apt-get install libqrencode-dev
./autogen.sh
./configure
make install


Set up SSD:

sudo lshw -short -C disk
sudo mkdir /media/[name_your_ssd]
sudo mount /dev/sdb1 /media/[name_your_ssd]
df -H


Run Bitcoind on SSD:

cd ~/bitcoin
sudo bitcoind -datadir=$HOME/media/[name_your_ssd]/

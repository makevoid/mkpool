# MKPool

### Commands to set up a pool on Debian 7


apt-get update
apt-get upgrade

apt-get vim -y

# for virtualbox only:
apt-get install build-essential dkms linux-headers-amd64

# to compile and run clients ##########

apt-get install libgmp-dev -y

vim /etc/apt/sources.list
# add: deb http://ftp.debian.org/debian sid main
apt-get update

apt-get -t sid install  libc6-dev libc6-dbg -y

vim /etc/apt/sources.list
# recomment the line
apt-get update

# other client

apt-get install libcurl3-gnutls


# ####################################


# standard stuff

apt-get install build-essential -y
apt-get install lm-sensors -y

# configure ssh and add lines:
vim /etc/ssh/sshd_config
# ControlPersist 4h
# ServerAliveInterval 60
# ServerAliveCountMax 10
# # TCPKeepAlive no # only for old routers


# MPOS guide with litecoin: https://github.com/MPOS/php-mpos/wiki/Quick-Start-Guide

apt-get install git libboost-all-dev libcurl4-openssl-dev libdb5.1-dev libdb5.1++-dev mysql-server -y

# give a (short) pass to mysql (we will accept localhost connections only)



# litecoin specific ########

cd ~
git clone git://github.com/litecoin-project/litecoin.git

# dependencies
apt-get install libminiupnpc-dev -y

# opt: switch to a more stable branch? es: 0.8.5.3-rc8 ?

cd litecoin/src

# fix from : https://bbs.archlinux.org/viewtopic.php?pid=1126374
vim  /usr/include/boost/thread/xtime.hpp
# rename TIME_UTC to TIME_UTC_

# pakages needed??
apt-get install libleveldb-dev libleveldb1 -y


# if compile doesnt works compile it anywhere else and copy it to the server [current working solution]
# copy: scp -r litecoin root@pool.mkvd.net:~/

make -f makefile.unix USE_UPNP=-

sudo cp litecoind /usr/bin


# link litecoind binary
ln -s /root/litecoin/src/litecoind /usr/bin


# testnet network
cd ~
git clone git://github.com/xrobau/litecoin-testnet-box.git
cd litecoin-testnet-box

vi 1/litecoin.conf
# edit username and password

# stratum mining #######

cd ~
apt-get install python-twisted python-mysqldb python-dev python-setuptools python-memcache python-simplejson -y
easy_install -U distribute

mkdir stratum
cd stratum

git clone https://github.com/Tydus/litecoin_scrypt.git
git clone https://github.com/ahmedbodi/stratum-mining.git
git clone https://github.com/ahmedbodi/stratum.git


cd stratum-mining
git submodule init
git submodule update

cd externals/litecoin_scrypt
python setup.py install

cd ~
cd stratum/stratum-mining/externals/stratum
python setup.py install


cd ~
cp stratum/stratum-mining/conf/config_sample.py stratum/stratum-mining/conf/config.py
vi stratum/stratum-mining/conf/config.py
# edit: wallet address, coindaemon and mysql settings [https://github.com/MPOS/php-mpos/wiki/Quick-Start-Guide#wiki-configuration]
# db_name: stratum_pool
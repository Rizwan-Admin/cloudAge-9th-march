PreRequisite
-------------

### CDP7 PreRequisite

* [Refer Here](https://docs.cloudera.com/cdp-private-cloud-base/7.1.8/installation/topics/cdpdc-installation.html) for CDP Installation steps 

* [Refer Here](https://docs.cloudera.com/cdp-private-cloud-base/7.1.8/installation/topics/cdpdc-before-you-install.html) for PreRequisite CDP Installation 

* [Optimizing Performance in Cloudera Runtime](https://docs.cloudera.com/cdp-private-cloud-base/7.1.9/managing-clusters/topics/cm-optimizing-performance.html)


```
sudo yum install wget -y
sudo yum install wget -y

```

* Install Java 8 
```
sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```

* Install Python 3.8 

```
sudo -i 

yum install gcc openssl-devel bzip2-devel libffi-devel zlib-devel -y

cd /opt

curl -O https://www.python.org/ftp/python/3.8.12/Python-3.8.12.tgz

tar -zxvf Python-3.8.12.tgz

cd /opt/Python-3.8.12

./configure --enable-shared

make

make altinstall

cp --no-clobber ./libpython3.8.so* /lib64/

chmod 755 /lib64/libpython3.8.so*

echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib/" >> ~/.bashrc

source ~/.bashrc 

ln -s /usr/local/bin/python3.8 /usr/bin/python3.8

ln -s /usr/bin/python3 /usr/local/bin/python3.8

ln -s /usr/local/bin/python3.8 /usr/bin/python3

```

* VM Swappiness & THP 

```
sudo sysctl -a | grep vm.swappiness

 sudo su -c 'cat >>/etc/sysctl.conf <<EOL
 'vm.swappiness=1' 
EOL'

sudo sysctl -p

sudo sed -i '/exit 0/d' /etc/rc.local

sudo su -c 'cat >>/etc/rc.local <<EOL
if test -f /sys/kernel/mm/transparent_hugepage/enabled; then
  echo never > /sys/kernel/mm/transparent_hugepage/enabled
fi
if test -f /sys/kernel/mm/transparent_hugepage/defrag; then
   echo never > /sys/kernel/mm/transparent_hugepage/defrag 
fi
exit 0
EOL'

sudo -i

source /etc/rc.local
```


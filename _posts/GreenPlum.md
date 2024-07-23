---
title: Installing GreenPlum & Python3.7 on Ubuntu Server
tags: [greenplum, postgresql, python37, ubuntu]
categories: [ubuntu]
date: [2023-05-22 21:00:00]
index_img: /img/greenplum.png
---



# Installing GreenPlum & Python3.7 on Linux(Ubuntu) Server

首先配置SSH

```shell
# 在服务器端
$ ls ./.ssh
> authorized_keys
```

将本机的id_rsa.pub内容复制到authorized_keys中

然后可以使用Xshell直接连接，选择私钥的时候选择配对的id_rsa文件即可



## 安装GreenPlum

```shell
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:greenplum/db
$ sudo apt update
$ sudo apt install greenplum-db-6
```

此时已经安装好了，我们查看安装的版本

```shell
$ ls /opt
greenplum-db-6.24.3
```

配置环境变量

```shell
$ source /opt/greenplum-db-6.24.3/greenplum_path.sh
$ which gpssh
/opt/greenplum-db-6.24.3/bin/gpssh
```

将初始化文件拷贝到用户目录下

```shell
$ cp $GPHOME/docs/cli_help/gpconfigs/gpinitsystem_singlenode .
$ ls
gpinitsystem_singlenode
```

将主机名写入文件

```shell
$ hostname
node0.dingzr.risb-pg0.utah.cloudlab.us
# 创建新文件hostlist_singlenode
$ vim hostlist_singlenode
# 将hostname的输出直接粘贴到里面
```

创建节点对应的文件夹

```shell
$ mkdir greenplum
$ cd greenplum
$ mkdir primary master
$ cd ..
```

修改初始化文件

```shell
$ vim gpinitsystem_singlenode
# declare -a DATA_DIRECTORY=(/users/dingzr/greenplum/primary /users/dingzr/greenplum/primary)
# MASTER_HOSTNAME=node0.dingzr.risb-pg0.utah.cloudlab.us
# MASTER_DIRECTORY=/users/dingzr/greenplum/master
```

```shell
$ gpssh-exkeys -f hostlist_singlenode
[STEP 1 of 5] create local ID and authorize on local host

[STEP 2 of 5] keyscan all hosts and update known_hosts file

[STEP 3 of 5] retrieving credentials from remote hosts

[STEP 4 of 5] determine common authentication file content

[STEP 5 of 5] copy authentication files to all remote hosts

[INFO] completed successfully
$ gpinitsystem -c gpinitsystem_singlenode
```

测试是否安装成功

```shell
$ createdb demo
$ psql demo
psql (9.4.26)
Type "help" for help.

demo=#
```



## 安装python3.7

wget工具下载压缩包

```shell
$ wget https://www.python.org/ftp/python/3.7.9/Python-3.7.9.tgz
$ tar -xzvf Python-3.7.9.tgz
$ ls
gpAdminLogs  gpinitsystem_singlenode  greenplum  hostlist_singlenode  Python-3.7.9  Python-3.7.9.tgz
```

进入解压后的python文件夹，安装python依赖库，并编译安装python

```shell
$ cd Python-3.7.9
# 安装依赖库（非常重要，不然有可能报各种奇奇怪怪的错！）
$ sudo apt-get install libbz2-dev libffi-dev libncurses5-dev libgdbm-dev liblzma-dev sqlite3 libsqlite3-dev openssl libssl-dev tcl8.6-dev tk8.6-dev libreadline-dev zlib1g-dev uuid-dev
# 有些时候安装完python后运行某些代码可能会出现for ifunc symbol `clock_gettime' segmentation fault这样的问题，我个人是这样解决的(参照这个帖子：https://stackoverflow.com/questions/58077672/python3-relink-issue-while-importing-opencv)：
# $ sudo apt install python3-opencv

# 编译，注意可以在--prefix后指定目录，也可以不指定
$ ./configure --prefix=/usr/local/src/python37
# 安装
$ sudo make && sudo make install
# 链接，注意这里面的/usr/local/src/python37需要换成编译阶段--prefix后面指定的目录
$ sudo ln -s /usr/local/src/python37/bin/python3.7 /usr/bin/python3.7
$ sudo ln -s /usr/local/src/python37/bin/pip3.7 /usr/bin/pip3.7
```

然后需要重新source以下，并重设PYTHONHOME和PYTHONPATH，因为被greenplum改过

```shell
$ source /opt/greenplum-db-6.24.3/greenplum_path.sh
$ unset PYTHONHOME
$ unset PYTHONPATH
```

终于弄好了，至少我是这样的，很麻烦，如果有别的问题建议google一下
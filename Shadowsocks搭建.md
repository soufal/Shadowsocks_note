# Shadowsocks搭建  

## 安装：

```
apt-get install build-essential python-pip python-m2crypto python-dev python-gevent supervisor vim
pip install shadowsocks  
```

## 配置shadowsocks：

```
vim /etc/shadowsocks.json
```

配置服务器内容：

```
{
    "server":"45.32.15.178",
    "port_password":{
        "8000":"soufal",
        "8001":"soufal1",
    },
    "timeout":600,
    "method":"aes-256-cfb"
}
```

## 配置SuperVisor：

```
vim /etc/supervison/conf.d/shadows.conf
```

```
[program:shadowsocks]
command=ssserver -c /etc/shadowsocks.json
autorestart=true
user= nobody
```

重启Supervisor使其配置生效：

```s
service supervisor start
surpervisorctl reload
```



## 优化配置：    

终端输入

```
vim /etc/security/limits.conf
```

在文件末尾，添加：

```
* hard nofile 51200
* soft nofile 51200
```

```
vim /etc/profile
```

输入：

```
ulimit -n 51200
```

终端输入：

```
vim /etc/sysctl.d/local.conf  
```

添加：

```
# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
# net.ipv4.tcp_congestion_control = hybla

# for low-latency network, use cubic instead
# net.ipv4.tcp_congestion_control = cubic
```

在终端输入，重新载入系统配置，然后重启系统：

```
sysctl --system
reboot
```

这样服务器端的Shadowsocks就配置完成了，在windows客户端上填写相应的内容即可。
# `ECS`日常维护
## 1、端口排查

1. 查看所有端口情况：
```bash
netstat -tln  
```
2. 查看特定端口情况
```bash
netstat -tln | grep 6800
```
3. 查看端口进程PID
```bash
lsof -i:6800
```
4. 强制结束进程
```bash
kill -9 PID  
```

## 2、启动各项服务
1. 启动`ariar2`服务
```bash
cd /root/Download/yaaw
nohup python -m SimpleHTTPServer 8888 &
cd download
nohup aria2c --enable-rpc --rpc-listen-all=true --rpc-allow-origin-all &
```
2. 启动`jupyterlab`服务
```bash
cd /root/JupyterLab
nohup jupyter lab &
```
3. 启动`bbr`加速
```bash
# 查看brr是否加载
lsmod | grep bbr
```
4. 启动`ssr`服务
> 实际上我用的是国内的服务器...翻不了墙，开了`ipv6`隧道也`ping`不上，所以就当练习吧，不过访问国内资源，下载什么的还是靠的住的...

```bash
启动：/etc/init.d/shadowsocks-r start
停止：/etc/init.d/shadowsocks-r stop
重启：/etc/init.d/shadowsocks-r restart
状态：/etc/init.d/shadowsocks-r status

配置文件路径：/etc/shadowsocks-r.json
日志文件路径：/var/log/shadowsocks-r.log
代码安装目录：/usr/local/shadowsocks-r
```

5. 启动`nginx`服务
```bash
先进入到 /usr/local/nginx/sbin/ 目录下，
启动 ./nginx
停止 ./nginx -s stop
重启 ./nginx -s reload
配置文件在：/usr/local/nginx/conf/nginx.conf
```

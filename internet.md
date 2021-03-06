# 彻底解决Ubuntu 16.04 联网问题

> 持续更新...

## 1、`mentohust`锐捷认证客户端
下载地址：http://c7.gg/aCFu4

```bash
sudo apt-get install mentohust
sudo mentohust -k
sudo mentohust -uusername -p123456 -a1 -d2 -b2 -v4.10 -w
```

## 2、修改`hosts`并启用`ipv6`
> 本文在`Ubuntu 16.04`测试通过，可以访问`Google、Facebok、Twitter、维基百科`等外网，其他平台`hosts`文件详见: [https://github.com/googlehosts/hosts](https://github.com/googlehosts/hosts/wiki/%E5%90%84%E5%B9%B3%E5%8F%B0-hosts-%E6%96%87%E4%BB%B6%E4%BD%8D%E7%BD%AE)

### 2.1 使用`ipv6`的`host`

1. 启动`ipv6`
```bash
sudo apt-get install miredo
sudo gedit /etc/default/ufw
```
将`IPV6=no`改为`IPV6=yes`
```bash
sudo gedit /etc/sysctl.d/10-ipv6-privacy.conf
# 将这两行改为0
net.ipv6.conf.all.use_tempaddr = 2
net.ipv6.conf.default.use_tempaddr = 2
```

2. 测试`ipv6`
```bash
ping6 ipv6.baidu.com
```

3. **校园网**是动态分配的`ipv6`地址，需要改成静态的。
```bash
sudo geidt /etc/sysctl.d/10-ipv6-privacy.conf
```
将`net.ipv6.conf.default.use_tempaddr`改为`0`
```bash
sudo sysctl --system
```

4. 修改`hosts`: [IPV6 hosts](https://github.com/lennylxx/ipv6-hosts)

```
sudo su
curl https://github.com/lennylxx/ipv6-hosts/raw/master/hosts -L >> /etc/hosts
```

5. 刷新配置
```bash
sudo sysctl --system
```

### 2.2 `hosts`地址
* [ipv4-hosts](https://github.com/googlehosts/hosts)
* [ipv6-hosts](https://github.com/StevenBlack/hosts)
* [可自行扩展的host](https://github.com/StevenBlack/hosts)

## 3、修改下载源

### 3.1 `apt-get`下载源
1. 首先测试适合系统最快的源：
![](https://raw.githubusercontent.com/ds19991999/githubimg/master/picgo/20181104131030.png)
它会自动匹配最佳的源。

2. `Google搜索`该源地址并修改源
```bash
sudo gedit /etc/apt/sources.list
```

### 3.2 更换`pip`源
`pip`国内的一些镜像,换源之后出现python2版本过低的情况导致以前的包下载不了，那就直接将文件夹删除，就能恢复原来的源。

* 阿里云 http://mirrors.aliyun.com/pypi/simple/ 
* 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
* 豆瓣(douban) http://pypi.douban.com/simple/ 
* 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/ 
* 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/


**linux**:

```bash
cat > ~/.pip/pip.conf
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

**windows**:
> 直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

```bash
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

### 3.3 更换`GitHub`源
> 其实改`hosts`的时候已经自动改好了，你可以进去查看一下.

```bash
sudo gedit /etc/hosts
219.76.4.4 github-cloud.s3.amazonaws.com
151.101.72.249 http://global-ssl.fastly.Net
192.30.253.112 http://github.com
```
### 3.4 更换`npm`源
* 临时使用:

```bash
npm --registry https://registry.npm.taobao.org install express
```

* 永久使用
```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

* 验证：
```bash
npm config get registry
or
npm info express
```

### 3.5 更换`yarn`源
> 安装源和原来 npm 是一样的，可以通用

```bash
yarn config set registry 'https://registry.npm.taobao.org'
```
验证：
```bash
yarn config get registry
```

## 4、与云服务器传输文件
为了避免每次传输文件的时候都要输入公网ip和密码挺麻烦的，所以索性就直接在服务器端配置本地`ssh`传输公匙，一劳永逸。
### 4.1 配置密匙验证
```bash
ssh-keygen -t rsa -C "your_email@example.com"
```
1. 在服务器端用户目录创建文件`~/.ssh/authorized_keys`，把本地`id_rsa.pub`公匙添加到这个文件中就可以了。
2. 在本地用户目录创建文件`~/.ssh/config`，添加如下信息：
```bash
host ecs
   user root
   hostname 公网ip
   port 22
   identityfile ~/.ssh/id_rsa
```
其中hostname是公网ip，`host`与之后的`git`服务器名对应。

### 4.2 传输单个文件
1、从服务器上下载文件
```bash
scp ecs:/path/filename /local_dir_path
```
2、上传本地文件到服务器
```bash
scp /path/filename ecs:/path   
```
### 4.3 传输文件夹
1、从服务器下载整个目录
```bash
scp -r ecs:/remote_dir /local_dir
```
2、上传目录到服务器
```bash
scp  -r /local_dir ecs:/remote_dir
```

## 5、修改`DNS`
修改`DNS`是为了进一步提高浏览器查询`ip`地址的速度，提高网络流畅度。
```bash
sudo gedit /etc/network/interfaces
# 加上
dns-nameservers 8.8.8.8
dns-nameservers 240c::6666
sudo gedit /etc/resolv.conf
# 加上
nameserver 8.8.8.8
nameserver 240c::6666
```
* `8.8.8.8`是谷歌主`DNS`服务器，最受欢迎
* `240c::6666`是国内首个`ipv6 DNS服务器`
* 当然你也可以选用其他`dns`服务器

```bash
sudo /etc/init.d/resolvconf restart
```
此时重启`DNS`服务发现又没有了，解决办法是：在`/etc/resolvconf/resolv.conf.d/`目录下创建`tail`文件，写入 
```bash
nameserver 8.8.8.8 
nameserver 240c::6666
```
这样在执行`sudo /etc/init.d/resolvconf restart`就`OK`了.

## 6、`SSR`服务器搭建并配置`ipv6`隧道代理

1. 申请`ipv6`的`ip`: https://www.tunnelbroker.net/register.php
> 注意几个`ip`的区别

![](https://raw.githubusercontent.com/ds19991999/githubimg/master/picgo/20181106175115.png)

2. 按照网上教程一件脚本配置搞定.
    
## 7、搭建`aria2`服务器
![](https://raw.githubusercontent.com/ds19991999/githubimg/master/picgo/20181106181826.png)
> 我的服务器地址，https://download.creat.kim , 你们可以上去看看，就是按照作者的教程搭的，我搭建的没有提供公共下载服务。

* 项目地址：[yaaw]https://github.com/binux/yaaw

* 开启服务：
```bash
nohup aria2c --enable-rpc --rpc-listen-all=true --rpc-allow-origin-all &
python -m SimpleHTTPServer 端口号 &
```

## 8、`nginx`多端口不同域名配置
> 直接在配置文件加个`server`函数搞定

* 我的配置文件：[nginx](https://github.com/ds19991999/useful-file/blob/master/Jupyter/nginx.conf)
* 效果：

![](https://raw.githubusercontent.com/ds19991999/githubimg/master/picgo/www1.gif)


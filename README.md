# shadowsocks and plugin
之前参考[博客](https://www.mkswp.com/shadowsocks-server-setup/)搭建ubuntu下的ss，用的挺舒适，但差不多一个月后，ss突然一直超时，ping和ssh都可以访问服务器，然后参考[回答](https://github.com/shadowsocks/shadowsocks-windows/issues/2228)，又能成功访问了，记录一下。
- server: 
  - ubuntu amd64
  - `sudo apt-get install shadowsocks`
- client: 
  - ubuntu amd64
  - `sudo apt-get install shadowsocks`
## [GoQuiet](https://github.com/cbeuw/GoQuiet/wiki/GoQuiet)
因为github的gist被墙，无法在国内下载GoQuiet的relese版本，将需要用到的可执行文件保存到本仓库的GoQuiet文件夹下。

- server
  - 修改sss-config.json文件
  ```json
    {
        "server":"0.0.0.0",
        "server_port":17163,//任意指定的未被占用的端口
        "local_address":"127.0.0.1",
        "local_port":1080,
        "password":"自定义密码",
        "timeout":300,
        "method":"aes-256-cfb",
        "fast_open":true
    }
  ```
  - 修改gq-server-config.json文件
  ```json
    {
        "WebServerAddr":"104.80.250.61:443",//当流量不来自于shadowsocks时的重定向服务器，设置为客户端配置中ServerName域名的IP记录。
        "Key":"自定义Key",
        "FastOpen":true
    }
  ```
  - scp拷贝`GoQuiet\server`下的所有文件到服务器里,假设保存在`~/server/`文件夹下。
  - 在~/.bashrc里添加
  ```bash
    #gq-ss
    alias gq-server="~/server/gq-server-linux-amd64-1.2.2 -r 127.0.0.1:8388 -c gq-server-config.json"
    alias ss-server="ssserver -c ~/server/sss-config.json -s 127.0.0.1 -p 8388"#端口号和gq-server一致
    alias gq-ss="gq-server & ss-server"
  ```
  - `source ~/.bashrc`
- client
  - 修改ssc-config.json文件
  ```json
    {
        "server":"服务器地址",
        "server_port":17163,//和服务器一致
        "local_address":"127.0.0.1",
        "local_port":1080,
        "password":"密码",//和服务器一致
        "timeout":300,
        "method":"aes-256-cfb",//和服务器一致
        "fast_open": true
    }
  ```
  - 修改gq-client-config.json文件
  ```json
    {
        "ServerName":"www.huawei.com",//符合服务器指定IP记录的实际域名
        "Key":"自定义的Key",//和server一致
        "TicketTimeHint":3600,
        "Browser":"chrome",
        "FastOpen":true
    }
  ```
  - scp拷贝`GoQuiet\client`下的所有文件到客户端里,假设保存在`~/client/`文件夹下。
  - 在~/.bashrc里添加
  ```bash
    # gq-ss
    alias gq-client="~/client/gq-client-linux-amd64-1.2.2 -s 服务器地址 -l 1984 -c ~/client/gq-client-config.json"
    alias ss-client="sslocal -c ~/client/ssc-config.json -s 127.0.0.1 -p 1984 -l 1080"#-p的参数和gq-client里-l的参数一致
    alias gq-ss="gq-client & ss-client"
  ```
  - `source ~/.bashrc`

## 使用
- sever
  - 终端输入`gq-ss`，不关闭该终端
- client
  - 终端输入`gq-ss`，不关闭该终端
  - 若`gq-ss`出错，显示端口问题
    - `lsof -i:1984` #显示占用1984端口号的PID,假设该PID号为111
    - `kill -9 111` #关闭PID号为111的进程
    - `lsof -i:1080` #显示占用1080端口号的PID,假设该PID号为222
    - `kill -9 222` #关闭PID号为222的进程
    - `gq-ss`

完成!!! :smile: :smile: :smile:

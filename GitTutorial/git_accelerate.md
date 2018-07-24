---
export_on_save:
 html: true
---
@import "/blog_head.md"

# github 加速访问速度

国内由于某些特殊原因，输入可以访问 github，但是速度也是相当地令人无语。

如下图是没有经过加速的 `git clone` 速度：

![](/GitTutorial/20180724144830.jpg)
其中 46KB/s 是在速度较快的时候，如果速度慢时，可能只有几字节每秒甚至根本下不动。

下面这个图是通过一系列措施加速后的速度：
![](/GitTutorial/20180724145228.jpg)

速度是 3M/s，加快速度可达上百倍！

## 第一步：修改 hosts

首先，通过 <https://www.ipaddress.com> 网站查询 `github.com` 的 ip 地址，有时候，本机得到的是一个无效的 IP，这时候如果 `ping github.com` 是 ping 不通的：

![](/GitTutorial/20180724151514.jpg)

如果连 ping 都不能 ping 通，那么根本就不能连接到 github，`git clone` 命令感觉就是被卡住的：

![](/GitTutorial/20180724152734.jpg)

通过 <https://www.ipaddress.com> 网站查询 `github.com` 的 ip 地址为 **192.30.253.112**，该地址可能会改变，所以最好自己查一下：

![](/GitTutorial/20180724153214.jpg)

之后，在 `/etc/hosts` 文件中加入一行：`192.30.253.112  github.com`

其 clone 速度有所提高：

![](/GitTutorial/20180724153735.jpg)

在将加速的 ip 也加入到 hosts 中：
`151.101.88.249 github.global.ssl.fastly.net`

最终 `/etc/hosts` 的内容类似于：
```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost

192.30.253.112  github.com
151.101.228.249  github.global-ssl.fastly.Net
```

修改 hosts 文件后的 clone 速度为：
![](/GitTutorial/20180724154232.jpg)

速度已经非常快了！如果你的机子也到达了上面的速度，我觉得第二步就没有必要继续看了。

## 第二步：使用 vpn 再度加速

如果你使用了 Shadowsocks 进行科学上网的话，可以在用 vpn 再度加速，命令为：
```
git config --global https.proxy 'socks5://127.0.0.1:1080'
git config --global http.proxy 'socks5://127.0.0.1:1080'
```
速度为：
![](/GitTutorial/20180724154732.jpg)

速度大于 2MB/s！

当然，撤销配置可以使用命令：
```
git config --global --unset https.proxy
git config --global --unset http.proxy
```

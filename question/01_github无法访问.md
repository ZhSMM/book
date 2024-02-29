# github无法访问
---

- [修改hosts文件](#修改hosts文件)
- [通过HTTP代理](#通过http代理)


---

#### 修改hosts文件

通过配置hosts来避免DNS污染导致的无法访问github：

1. DNS查询，输入 github.com：[https://tool.chinaz.com/dns/](https://tool.chinaz.com/dns/)

   ![image-20240124125533357](images/image-20240124125533357.png) 

2. 修改系统的hosts文件：

   ```shell
   # 文件位置：C:\Windows\System32\drivers\etc\hosts
   # 添加记录
   140.82.121.4 github.com
   ```

3. 刷新DNS：

   ```shell
   # 在cmd窗口执行
   ipconfig /flushdns
   ```



#### 通过HTTP代理

如果存在可用的代理，可以通过设置代理来连接GitHub：

1、全局代理：保存在`~/.gitconfig`文件中

```shell
# 查看代理配置
git config --global --get http.proxy
git config --global --get https.proxy

# 设置http代理：
# git config --global http.proxy http://proxyUsername:proxyPassword@proxy.server.com:port
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# 设置socks5代理
git config --global http.proxy 'socks5://127.0.0.1:7891'
git config --global https.proxy 'socks5://127.0.0.1:7891'

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

2、设置仅针对某个网站的代理：

```shell
# 只对github.com
git config --global http.https://github.com.proxy http://127.0.0.1:7890
git config --global http.https://github.com.proxy socks5://127.0.0.1:7891

# 取消代理
git config --global --unset http.https://github.com.proxy
```

3、仅在clone指定的存储库中使用代理，在其他存储库中不需要时：

```shell
# git clone https://仓库地址 --config "https.proxy=proxyHost:proxyPort"
git clone https://github.com/user/demo.git --config https.proxy=https://127.0.0.1:7890
```

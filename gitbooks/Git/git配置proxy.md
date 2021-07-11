# git clone 走ss设置
> 设置代理
```
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890
git config --global http.sslVerify false
```

> 取消代理
 ```
git config --global --unset http.proxy 
git config --global --unset https.proxy
```
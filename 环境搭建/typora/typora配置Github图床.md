## 1. 配置GitHub图床方法

https://blog.csdn.net/weixin_49163601/article/details/124704955



## 2. GitHub图片已上传但不显示解决方法

1. 打开域名解析网站： https://ping.eu/nslookup
2. 然后输入 raw.githubusercontent.com，如下图：

打开本机的 hosts 文件，其路径如下：
**`C:\Windows\System32\drivers\etc\hosts`**, 然后将网站上解析出的raw.githubusercontent.com的IP添加到hosts文件中：

185.199.110.133    raw.githubusercontent.com
185.199.109.133    raw.githubusercontent.com
185.199.108.133    raw.githubusercontent.com
185.199.111.133    raw.githubusercontent.com
---
  layout: single
  title:  "Let's Encrypt为nginx添加https时遇到的坑"
  date:   2017-10-19 17:00:16 +0800
  categories: Let's-Encrypt
  comments: true
---
## 安装certbot

```shell
$ sudo yum install certbot
```
遇到的问题：yum提示找不到certbot包

原因: 通常我们遇到`yum install`提示没有可用软件包时，是因为CentOS自带源中提供软件包数量有限，我们需要为CentOS安装EPEL源，EPEL源中包含上万个软件。

解决方法:

1. 安装EPEL
```shell
$ sudo yum install epel-release
```
2. 检查yum源中是否已加入EPEL
```shell
$ sudo yum repolist
repo id                     repo name                                                                status
base/7/x86_64               CentOS-7 - Base                                                          9,590+1
elrepo-kernel               ELRepo.org Community Enterprise Linux Kernel Repository - el7                 33
epel/x86_64                 Extra Packages for Enterprise Linux 7 - x86_64                            11,992
extras/7/x86_64             CentOS-7 - Extras                                                            227
updates/7/x86_64            CentOS-7 - Updates                                                         738+3
repolist: 22,580
```
从上面我们可以看到，yum源中已经包含epel了，并且epel源中共包含11992个软件包。

如果你的repolist中没有epel，那就需要手动配置一下，添加以下代码：
```shell
$ vi /etc/yum.repos.d/epel.repo
[epel]
name=ExtraPackages for Enterprise Linux 7 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/7/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7

[epel-debuginfo]
name=ExtraPackages for Enterprise Linux 7 - $basearch - Debug
#baseurl=http://download.fedoraproject.org/pub/epel/7/$basearch/debug
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-debug-7&arch=$basearch
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=1

[epel-source]
name=ExtraPackages for Enterprise Linux 7 - $basearch - Source
#baseurl=http://download.fedoraproject.org/pub/epel/7/SRPMS
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-source-7&arch=$basearch
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=1
```
如果你的`epel.repo`文件中已经有这段内容，那么需要将[epel]配置块中的enable=0改成enable=1。


## 安装certbot提供的nginx插件

```shell
$ sudo certbot --nginx
```
遇到的问题:
`The requested nginx plugin does not appear to be installed`

原因:
我也是google中搜到的解决方法，并不太明白具体原因

解决方法:
先用yum安装python-certbot-nginx,之后再执行上面的命令就可以成功安装certbot提供的nginx插件了
```shell
$ sudo yum install python-certbot-nginx
```

以上就是我在使用Let's Encrypt为自己的博客配置https时与到的坑，希望对大家有所帮助。如需要完整的配置流程，强烈推荐`康上明学`的[`Let's Encrypt 给网站加 HTTPS 完全指南`](https://ksmx.me/letsencrypt-ssl-https/)


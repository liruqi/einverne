---
layout: post
title: "GitHub 使用gpg签名"
tagline: ""
description: ""
category: 经验总结
tags: [github, git, gpg, linux, ]
last_updated: 
---

Ubuntu 下，GnuPG 2.0 的支持都在 `gnupg2` 这个[packages](http://packages.ubuntu.com/search?keywords=gnupg2) 下，通过下面命令安装:

    sudo apt-get install gnupg2

GitHub 要求使用 GnuPG 2.1 及以后的版本。


## 生成GPG签名
使用 

    gpg2 --full-gen-key

1. 选择默认 RSA and RSA
2. 推荐 4096
3. 过期时间
4. 相关信息包括 Real name, Email, Comment
5. 密码

使用以下命令查看本地密钥

    gpg2 --list-keys --keyid-format LONG

结果

```
/home/einverne/.gnupg/pubring.gpg
---------------------------
sec   rsa4096/F80B65AAAAAAAAAA 2018-01-31 [SC]
uid                 [ultimate] Ein Verne (co) <email@address>
ssb   rsa4096/B63A4CAAAAAAAAAA 2018-01-31 [E]
```

将其中的第三行 sec 中 `rsa4096` 以后的ID记住，拷贝出来。

然后使用

    gpg2 --armor --export ID

来获取 GPG KEY，拷贝 `-----BEGIN PGP PUBLIC KEY BLOCK-----` 和 `-----END PGP PUBLIC KEY BLOCK-----` 之前，包括这两行的内容到 GitHub。

## 配置GPG
产生GPG，并且已经添加到 GitHub 后台，那么需要本地配置，告诉 git 本地签名。查看本地 gpg

    gpg2 --list-keys --keyid-format LONG

添加配置

    git config --global user.signingkey F80B65AAAAAAAAAA
    git config --global gpg.program gpg2

将GPG添加到 bashrc

    echo 'export GPG_TTY=$(tty)' >> ~/.bashrc

## 签名 commit

在提交时使用 `-S` 选项

    git commit -S -m your commit message

来本地签名提交

提交标签同理

    git tag -s mytag

可以使用 `-v` 来验证

    git tag -v mytag

## reference

- <https://help.github.com/articles/signing-commits-with-gpg/>

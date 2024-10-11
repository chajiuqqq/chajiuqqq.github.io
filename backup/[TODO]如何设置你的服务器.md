> https://becomesovran.com/blog/server-setup-basics.html

# SSH
1. 创建新用户
```
sudo adduser newuser
sudo usermod -aG sudo newuser
```
> `sudo adduser newuser`
作用：
这个命令用于在系统中创建一个名为 newuser 的新用户账户。

> `sudo usermod -aG sudo newuser`
作用：
这个命令用于将已存在的用户 newuser 添加到 sudo 组中，从而赋予该用户使用 sudo 命令提升权限的能力。
-aG：这是两个选项的组合：
> - `-a（append`）：表示在不移除用户当前所属其他组的情况下，追加新的组。
> - `-G（groups）`：指定要添加用户到的一个或多个组。
`sudo usermod -aG sudo,admin alice`：将用户添加到一个或多个附加组中。

> usermod的其他参数：
 ```
sudo usermod -g developers alice #  将用户的主组更改为指定的组
sudo usermod -l newusername oldusername # 修改用户的登录名
sudo usermod -d /home/newhome -m alice # 修改用户的主目录路径，-m 可以同时移动现有的主目录内容到新的位置
sudo usermod -L alice   # 锁定账户
sudo usermod -U alice   # 解锁账户
sudo usermod -s /bin/zsh alice #修改用户的默认登录Shell。
sudo usermod -e 2024-12-31 alice #将用户 alice 的账户设置为在 2024 年 12 月 31 日过期。
sudo usermod -u 1500 alice #将用户 alice 的UID更改为 1500。
 ```

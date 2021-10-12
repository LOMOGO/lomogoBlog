# 添加 linux 新用户，并设置为管理员

```bash
# 1 新建用户
adduser <用户名>
# 2 切换到 root 模式，然后更改刚刚创建的用户的密码
passwd <刚刚设置的用户名>
# 3 将新创建的用户加入到 sudoers 文件中，即设置该用户为管理员
# 3.1 修改 sudoers 的权限，使可写
chmod +w /etc/sudoers
# 3.2 添加当前用户,在 /etc/sudoers 文件中搜索内容：root    ALL=(ALL)       ALL 并在改行下面一行输入：
lomogo  ALL=(ALL)       ALL
# /etc/sudoers 文件被修改后的效果应当是这样的：
...
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
lomogo  ALL=(ALL)       ALL

## Allows members of the 'sys' group to run networking, software,
...
# 4 最后恢复 /etc/sudoers 文件的原本的权限
chmod -w /etc/sudoers
```

至此，添加 linux 新用户，并设置其为管理员的操作完成。
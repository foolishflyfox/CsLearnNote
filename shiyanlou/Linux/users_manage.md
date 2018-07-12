---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 用户与文件权限管理

## 查看用户

`who` 命令；

`who -a` 打印所有能打印的内容；

`who am i` : 记录的是用户的“真身”，也就是即使使用`su` 改变用户身份，`who am i` 也不会改变显示内容；

`whoami` : 记录的是用户的“当前身份”，会因为`su` 命令而发生改变；

## su、su - 和 sudo

`su <user>` 切换到用户 user；
`su - <user>` 切换到用户 user，同时环境变量变为目标用户的环境变量；

## 用户增删

添加用户：`sudo adduser <new_user_name>`
删除用户：`sudo deluser --remove-home <user_name>`

**`adduser` 和 `useradd` 的区别**
- `useradd` 只创建用户， 创建完了以后用 `passwd <username>` 去设置新用户的密码。
- `adduser` 会创建用户，创建目录，创建密码，做着一系列的操作。

## 用户组

查看自己属于哪些用户组？

- `groups <username>`：默认查询的是当前身份所属组； **每次新建用户如果不指定用户组的话，默认会自动创建一个与用户名相同的用户组；** 默认情况下在 sudo 用户组中的用户可以使用 `sudo` 命令获得 root 权限；   或者是在 `/etc/sudoers.d` 中为用户赋予 `sudo` 权限。
![](/shiyanlou/Linux/assets/20180712143207.png)
- 查看 `/etc/group` 文件

将某个用户添加到一个用户组中：`sudo usermod -G <groupname> <usernamesu>`

## Linux 文件权限

### ls 命令内容

![](/shiyanlou/linux/assets/wm.png)

一个目录只有同时具有读权限和执行权限才能打开并查看内部文件，只有具有写权限才能在其中创建其他文件；


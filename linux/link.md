# linux 软链接、硬链接

理解软硬链接前首先要熟悉linux文件系统的存储方式。linux文件存储分为inode和data block，inode记录文件元数据（创建时间、修改时间、权限等），data block记录文件数据。目录的data block记录了该目录下的文件名和该文件名使用的inode号。

## 软链接（Symbolic Link）

软链接创建一个链接文件，该文件拥有自己的inode和data block，data block 记录了该文件指向的目标文件。若删除了目标文件，链接文件就会成为无效链接（dangling link）。

```
$ ln -s yi yi-sl
$ ll
total 0
-rw-rw-r--. 1 fintech fintech 0 Dec 11 11:03 yi
lrwxrwxrwx. 1 fintech fintech 2 Dec 11 11:03 yi-sl -> yi
```

## 硬链接（hard Link） 

硬链接在目录的data block新增一条文件名记录，该文件名指向已存在的inode号。所以硬链接实际不会创建一个新文件，不占用额外的inode和data block。不能对目录创建硬链接，硬链接不能跨文件系统。

```
$ ln yi yi-hl
[fintech@localhost luo]$ ll -i
total 0
1138246 -rw-rw-r--. 2 fintech fintech 0 Dec 11 11:03 yi
1138246 -rw-rw-r--. 2 fintech fintech 0 Dec 11 11:03 yi-hl
1138335 lrwxrwxrwx. 1 fintech fintech 2 Dec 11 11:03 yi-sl -> yi
```

注意两个文件的inode号（第一个字段）是相同的，同时inode的连接数（第三个字段）也会加1，表示有两个文件名连接到该inode。

参考：[鸟哥的 Linux 私房菜 -- Linux 磁盘与文件系统管理](http://cn.linux.vbird.org/linux_basic/0230filesystem.php)
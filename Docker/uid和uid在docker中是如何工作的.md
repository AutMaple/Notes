# uid 和 gid 

linux 内核负责管理 uid 和 gid 空间，出发系统调用后，uid 和 gid 会被用于检查是否有操作对应资源的权限。例如，当一个进程试图对某个文件写入内容时，内核会检查创建该进程的 uid 和 gid, 从而确定该进程是否有操作该文件的权限。权限校验使用的是 uid，而不是用户名。

## uid 和用户名的关系

权限控制依据的 uid，而不是用户名，用户名不是内核的一部分。uid 与用户名是一对多的关系，两个 uid 相同的用户所拥有的权限是一样。

> That's because the username (and group names) that show up in common linux tools aren't part of the kernel, but are managed by external tools (/etc/passwd, LDAP, Kerberos, etc). So, you might see different usernames, but you can't have different privileges for the same uid/gid, even inside different containers

## 查看用户的 uid

要查看用户的 uid 可以使用如下命令:

```shell
$ echo $UID # 某些 shell 中输出为空
$ cat /etc/passwd | grep <username>
```

例如我要查看用户名为 autmaple 的 uid:

```shell
cat /etc/passwd | grep autmaple
```

# docker 中的 uid 和 gid

在宿主机上运行容器时，docker 和宿主机共享一个内核。这也就意味着 docker 和宿主机共享内核维护的 uid 和 gid 空间。这样就会出现宿主机用户的用户名和容器中用户的用户名不相同，但是 uid 相同的情况。

## docker 容器出现文件权限不足

当我们启动容器但是未指定用户的情况下，默认使用 root 用户来运行即使用 uid = 0 的用户来运行的。由于安全问题，不推荐容器以 root 用户来运行

我们可以使用 `docker run -u` 指令来指定启动容器的用户，可以是宿主机中存在的用户名也可以是 uid

当操作数据卷出现权限不足的时候，可以查看容器的执行者和数据卷的所有者是否相同。
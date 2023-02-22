# 变量替换

在 docker-compose.yaml 文件中可以引用如下位置的变量：

- shell 中的环境变量
- docker-compose.yaml 所在文件夹的 `.env` 文件中定义的变量。`.env` 文件用于定义变量的默认值。shell 中定义的变量会覆盖 `.env` 文件中变量名相同的变量

引用的格式为 `${var_name}` 或者 `$var_name`，如果对应的变量不存在，则使用空字符串来替换。我们也可以通过 `${var_name:-default_value}` 的方式来定义变量的默认值

```ad-error
title: 注意

`.env` 文件仅在使用 `docker compose up` 命令时有效，`docker stack deploy` 命令无效
```

## 查看变量是否成功替换

要想查看变量是否成功替换，可以在 `docker-compose.yaml` 所在的文件夹中运行 `docker compose config` 命令。
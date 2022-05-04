# 各配置文件的作用

- `default.yaml`: 一个全局配置文件，在 `default.yaml` 配置文件中进行配置之后，其他的拼写方案共享这份配置
- `luna_pinpin.schema.yaml`： 明月拼音的配置文件，只对明月拼音有效，其他的拼音方案的配置文件：`方案名.schema.yaml`
- `weasel.yaml`: rime 的常规配置，用来定制外观
- `symbols.yaml`: 定义键盘中标点符号的映射

上述文件通常不直接修改，因为在修改它们之后，如果软件更新，则会覆盖我们的配置，rime 规定在如下的文件中进行自定义配置

- `default.custom.yaml`
- `lua_pinyin.custom.yaml`
- `weasel.custom.yaml`
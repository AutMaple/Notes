# 1. User：用户信息表

| 字段        | 类型     | 空   | 默认值  | 备注                         |
| ----------- | -------- | ---- | ------- | ---------------------------- |
| id          | int      | no   |         | 主键自增                     |
| name        | varchar  | no   |         | 用户名                       |
| password    | varchar  | no   |         | 密码                         |
| role        | int      | no   |         | 0：管理员<br />1：仓库操作员 |
| insert_date | datetime | no   | `now()` | 数据插入时间                 |

# 2. Product: 物品表

| 字段        | 类型     | 空   | 默认值  | 备注         |
| ----------- | -------- | ---- | ------- | ------------ |
| id          | int      | no   |         | 主键自增     |
| name        | varchar  | no   |         | 物品名称     |
| type_id     | int      | no   |         | 物品类型     |
| unit        | varchar  | no   |         | 物品单位     |
| price       | float    | no   |         | 物品价格     |
| number      | int      | no   |         | 物品数量     |
| description | varchar  | yes  |         | 对物资的描述 |
| inser_time  | datetime | no   | `Now()` | 数据插入时间 |
| user_id     | int      | no   |         | 操作员 id    |
| username    | varchar  | no   |         | 操作员的名字 |

# 3. Manipulation: 物品操作记录表

| 字段        | 类型     | 空   | 默认值  | 备注               |
| ----------- | -------- | ---- | ------- | ------------------ |
| id          | int      | no   |         | 自增主键           |
| product_id  | int      | no   |         | 物品的 id          |
| number      | int      | no   |         | 本次操作的物品数量 |
| comment     | varchar  | yes  |         | 备注               |
| insert_date | datetime | no   | `now()` | 数据插入的时间     |
| user_id     | int      | no   |         | 本次操作的用户 id  |
| username    | varchar  | no   |         | 本次操作的用户名   |

# 4. ProductType: 物品类型表

| 字段        | 类型     | 空   | 默认值  | 备注              |
| ----------- | -------- | ---- | ------- | ----------------- |
| id          | int      | no   |         | 自增主键          |
| name        | varchar  | no   |         | 物资类型          |
| comment     | varchar  | yes  |         | 备注              |
| insert_date | datetime | no   | `now()` | 数据插入的时间    |
| user_id     | int      | no   |         | 本次操作的用户 id |
| username    | int      | no   |         | 本次操作的用户名  |
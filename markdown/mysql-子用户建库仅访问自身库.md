# MySQL 子用户建库仅访问自身库

有若干个 MySQL 用户可以使用以下命令：`CREATE DATABASE name;` 但我希望这些用户中的每一个人都能够只查看和访问他们自己创建的数据库。

```sql
GRANT ALL PRIVILEGES ON user_％. * TO 'user'@'%';
```

问题是用户在命名数据库时必须非常小心。

例如：如果用户 `admin` 创建数据库 `root_test`，则用户 `root` 可以独占访问该数据库，而用户 `admin` 则不能。

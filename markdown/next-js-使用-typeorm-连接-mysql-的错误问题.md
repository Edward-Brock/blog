# Nest.js 使用 TypeORM 连接 MySQL 的错误问题

## 出现问题

使用 Nest.js 开发时，使用 TypeORM 连接 MySQL 时，配置了 `TypeOrmModule.forRoot()` ，却在运行项目时始终报错提示以下错误信息：

`ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)…  
Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client`

提示无法连接到数据库。

## 解决办法

问题在于 Node.js (mysql) 尚不支持 MySQL 8 这种新的默认身份验证方法。

一、（推荐）

把 `mysql` 更换为 `mysql2` 。

安装和使用 mysql2（而不是mysql）并使用它：

`npm i mysql2`  
`mysql = require('mysql2');`

二、（不推荐）

如果只是想消除错误，以冒项目安全风险为代价（例如，这只是个人项目或开发环境）则在 MYSQL Workbench 中执行以下查询

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';`

更新 root 用户配置，变更数据库加密模式。

然后运行此查询以刷新权限：

`flush privileges;`

这样做后尝试使用节点连接。

如果这不起作用，尝试不使用 `@'localhost'` 这部分。

## 参照文章

[MySQL 8.0 - Client does not support authentication protocol requested by server; consider upgrading MySQL client —— Stack Overflow](https://stackoverflow.com/questions/50093144/mysql-8-0-client-does-not-support-authentication-protocol-requested-by-server)

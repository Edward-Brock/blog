# Docker Mysql 8 自动定时备份

自服务器重新维护以后，上一次的 MySQL 数据库定时备份由于直接写在 crontab 中，导致这次再次测试却一直也不好使，反而导致我测试浪费了好几天。

这回从网上找到了其他方法，我今天测试了一下已经能够正常自动定时备份了。

## 创建备份文件

```shell
mkdir /data/backup
cd /data/backup
```

## 编写备份脚本代码

```shell
vim backup.sh
```

```shell
#!/bin/sh  #-h 后面改为自己的ip
#-u 后面改为自己的数据库账号
#-p 后面改为自己的数据库密码,有字符需要加""
#demand_database改为你想要备份的数据库名称
echo "开始备份数据库";
# 导出所有数据库 username 替换为自己mysql登陆名，password123登陆密码**
## mysqldump -h106.14.XX.XXX -uusername -p"password123" --all-databases > /data/mysqlbackup/databaseName`date +%Y-%m-%d_%H%M%S`.sql;

# 导出指定数据库并压缩
## mysqldump -h106.14.XX.XXX -uusername -p"password123"  demand_database| gzip > /data/mysqlbackup/databaseName`date +%Y-%m-%d_%H%M%S`.sql.gz;

# 最近转投docker怀抱，本地不安装mysql时，采用docker的mysql备份，备份语句修改为**
docker exec mysql sh -c 'exec mysqldump --all-databases -uUSERNAME -pPASSWORD --all-databases' > /data/backup/database_`date +%Y-%m-%d_%H%M%S`.sql;

# mysql 替换为对应的容器名
# 删除 3 天前的备份文件

backupdir=/data/backup
db_name=databaseName_

find $backupdir -name $db_name"*.sql.gz" -type f -mtime +3 -exec rm -rf {} \;


echo "备份完成";
```

测试以上代码需要根据自己的需求进行相应修改。

## 更改备份脚本权限

```shell
chmod +x dbbackup.sh
```

## 使用 `crontab` 定时执行备份脚本

```shell
crontab -e
```

输入上面的命令后会进入 vim 编辑的一个文件，在上面写 cron 表达式 + 脚本地址就行了

测试 每 1 分钟执行一次：

```shell
*/1 * * * *  /data/backup/backup.sh
```

我设定每 12 个小时更新一次，添加如下代码：

```shell
0 0 */12 * * /data/backup/backup.sh
```

## 参照文章

- [MySQL8 定时备份数据库](https://www.scarletdrop.cn/archives/18)
- [docker MySQL数据库的备份与还原，以及每天定时自动备份](https://blog.csdn.net/weixin_52270081/article/details/123552094)

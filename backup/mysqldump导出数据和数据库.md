> https://www.cnblogs.com/chenmh/p/5300370.html

1. 导出所有数据库
```
mysqldump -uroot -proot --all-databases >/tmp/all.sql
```

2. 导出指定数据库
```
mysqldump -uroot -proot --databases db1 db2 >/tmp/user.sql
```

3. 只导出表结构，不包含数据
```
mysqldump -uroot -proot --no-data --databases db1 >/tmp/db1.sql
```

4. 导出指定表
```
mysqldump -uroot -proot --databases db1 --tables a1 a2  >/tmp/db1.sql
```

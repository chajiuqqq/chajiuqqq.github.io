方式一：mysql -e
`mysql -e "source batch-file"`
方式二：mysql < batch-file
`mysql -h host -u user -p < batch-file`
对于这种方式，还可以查看文件执行时的输出，有如下两种方法：
```
mysql < batch-file | more
mysql < batch-file > mysql.out
```
方式三：mysql客户端命令行方式
先登录mysql客户端，然后运行如下语句：
`mysql> source /path/filename;`
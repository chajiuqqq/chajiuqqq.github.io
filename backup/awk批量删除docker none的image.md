1. 查看none镜像：
```
# docker image ls | grep none
<none>                        <none>    01a4d5898ad0   39 minutes ago      867MB
<none>                        <none>    950d8ca6051f   About an hour ago   973MB
<none>                        <none>    cce3f6491395   2 hours ago         940MB
<none>                        <none>    2e10b33cb6dd   3 hours ago         932MB
<none>                        <none>    d616e7abf40c   3 hours ago         932MB
<none>                        <none>    3565400a4b6e   4 hours ago         932MB
<none>                        <none>    1a5f77c032b9   4 hours ago         932MB
<none>                        <none>    a65b4e782623   4 hours ago         932MB
<none>                        <none>    7c6de7d06887   5 hours ago         932MB
<none>                        <none>    f2f5d096e2ed   5 hours ago         932MB
<none>                        <none>    8f93202d9599   20 hours ago        932MB
<none>                        <none>    6b802f894a86   21 hours ago        932MB
```

2. 过滤出imageID

- awk 命令会默认以空格作为字段分隔符，并将每行视为一个记录。
- {print $3} 指定打印每行的第三个字段。
- 如果你的数据以制表符分隔而不是空格，你可以使用 -F 选项来指定分隔符。`awk -F '\t' '{print $3}' data.txt`

```
# docker image ls | grep none | awk '{print $3}'
01a4d5898ad0
950d8ca6051f
cce3f6491395
2e10b33cb6dd
d616e7abf40c
3565400a4b6e
1a5f77c032b9
a65b4e782623
7c6de7d06887
f2f5d096e2ed
8f93202d9599
6b802f894a86
```

3. 删除镜像
```
# docker rmi $(docker image ls | grep none | awk '{print $3}')
Deleted: sha256:01a4d5898ad05041103939a1cf51034d138a93b069146f54bfe0c74e8a84c54f
Deleted: sha256:950d8ca6051fd3785254bb198d13a93ac05445c79081e2d2740001e875eac3f4
Deleted: sha256:cce3f64913953be4cf265e97ee4c294d1275860032b84ac7cb8dec98fc5c953c
Deleted: sha256:2e10b33cb6dd73afc4cd7258372b9ebf7ba8231fd42a5a62cade4232720beb07
Deleted: sha256:d616e7abf40c84f93246cc66c2374b94e9cc8070f7ad7cf449f912b220b53d97
Deleted: sha256:3565400a4b6edf3662618ec4156987f671196160441bca93bd0719d3ab067e6a
Deleted: sha256:1a5f77c032b9d3110eb267bc5d8ae58ac6625047330719e5fdb941d11455bfd1
Deleted: sha256:a65b4e7826236440005d7d452af5a374c7a9c8cb1b4bfb6f399d9cad606f2912
Deleted: sha256:7c6de7d06887fb44241a93b933961cb5bc33c6f5820577c104aeea8cf5410582
Deleted: sha256:f2f5d096e2edd6e78f9c32934f1292b2ac59fd68e40e8903ba34a163c7e32503
Deleted: sha256:8f93202d95990b66d21a1e324fbaf14a814e74be09cc39f53228452baa0469ab
Deleted: sha256:6b802f894a8681c4c3ebdfb199e517cbece07b6716272dfed47d1835d92c8782
```

## awk使用 //from gpt

`awk` 是一个功能强大的文本处理工具，它可以用于从文件或标准输入中提取、处理和操作数据。`awk` 使用一种脚本语言，可以根据指定的模式进行数据匹配和处理。

`awk` 的基本语法如下：

```shell
awk 'pattern { action }' file
```

其中，`pattern` 是用于匹配数据的模式，`action` 是在匹配成功时执行的动作。`file` 是要处理的文件名，可以省略，默认从标准输入中读取数据。

下面是一些常用的 `awk` 用法示例：

1. 打印整个文件：

```shell
awk '{print}' file
```

2. 按行打印文件：

```shell
awk '{print NR, $0}' file
```

3. 根据字段进行筛选和打印：

```shell
awk '$3 > 50 {print $1, $3}' file
```

4. 自定义字段分隔符：

```shell
awk -F ',' '{print $2}' file
```

5. 使用条件语句和循环：

```shell
awk '{if ($1 > 10) {print "Large"} else {print "Small"}}' file
```

6. 计算和统计数据：

```shell
awk '{sum += $1} END {print "Total:", sum}' file
```

这些只是 `awk` 的一些基本用法示例，实际上，`awk` 还提供了丰富的内置函数、数组和控制结构，可以进行更复杂的文本处理和数据操作。

请注意，`awk` 的语法和功能非常灵活，可以根据具体的需求进行定制和扩展。你可以根据自己的需要编写更复杂的 `awk` 脚本以满足特定的文本处理任务。
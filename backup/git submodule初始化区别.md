## 拉取子仓库的N种方法
 
- clone 主仓库时自动拉取子仓库，使用` --recurse-submodules`参数：`git clone --recurse-submodules https://github.com/chaconinc/MainProject`

- 先clone主仓库，然后手动拉取子仓库：


方法1：

```
git submodule init
# to initialize your local configuration file,

git submodule update
 # to fetch all the data from that project and check out the appropriate commit listed in your superproject（同步主仓库指定的子仓库版本）
```

方法2（效果一样，更方便）：
```
git submodule update --init
# 嵌套拉取
git submodule update --init --recursive
```

`git submodule update --remote`做了什么？

执行了两个命令，对子仓库的默认分支fetch然后merge，并指向该分支最新提交
# 用例
- **查看commit提交的细节，会展示文件变化细节**：`git show <commit>` OR `git log -p`
- **查看commit中提交的文件名称**：`git show --name-status <commit>`

```
$  git show --name-status 1e7cf253a6e6e
commit 1e7cf253a6e6ecee6376b12d9fcabf2ec3f7f9e6 (EH-1699)
Author: caijiachen <chajiuqqq@gmail.com>
Date:   Sat Sep 7 15:57:21 2024 +0200

    EH-1699: add other stock business interface to proto

M       oms/pkg/proto/oms_order_v3.pb.go
M       oms/pkg/proto/oms_order_v3.proto
M       rfcctl/types/mm.go
M       webproto
```
# git stash
```
 git stash -h
usage: git stash list [<log-options>]
   or: git stash show [-u | --include-untracked | --only-untracked] [<diff-options>] [<stash>]
   or: git stash drop [-q | --quiet] [<stash>]
   or: git stash pop [--index] [-q | --quiet] [<stash>]
   or: git stash apply [--index] [-q | --quiet] [<stash>]
   or: git stash branch <branchname> [<stash>]
   or: git stash [push [-p | --patch] [-S | --staged] [-k | --[no-]keep-index] [-q | --quiet]
                 [-u | --include-untracked] [-a | --all] [(-m | --message) <message>]
                 [--pathspec-from-file=<file> [--pathspec-file-nul]]
                 [--] [<pathspec>...]]
   or: git stash save [-p | --patch] [-S | --staged] [-k | --[no-]keep-index] [-q | --quiet]
                 [-u | --include-untracked] [-a | --all] [<message>]
   or: git stash clear
   or: git stash create [<message>]
   or: git stash store [(-m | --message) <message>] [-q | --quiet] <commit>
```
- 显示stash列表：` git stash list`
```
$ git stash list
stash@{0}: WIP on EH-1699: 1e7cf253 EH-1699: add other stock business interface to proto
stash@{1}: On dev: A bunch of stuff that didn't work
stash@{2}: WIP on EH-1651: 88be22bf EH-1651:change sync delivery order
stash@{3}: WIP on dev: 7b2cf784 EH-1593
stash@{4}: WIP on dev: db503df4 EH-1511: debug
stash@{5}: WIP on dev: db503df4 EH-1511: debug
stash@{6}: WIP on dev: ec0e86ad Merge remote-tracking branch 'origin/dev' into dev
stash@{7}: WIP on EH-1136: 70d7b5dd oms:db update
```
- 暂存staged变化 ：`git stash`
- 暂存staged和untracked文件： `git stash -u`
- 应用stash并从stash列表中删除：`git stash pop`
- 应用stash但不删除stash记录：`git stash apply`

# git reset 
- `git reset --soft <commit>`：撤回提交并把变化存入stage区，不会丢弃本地文件的其他更改。
- `git reset --mixed <commit>`：这是git reset的默认参数。撤回提交，Unstage提交过的文件。这样我们只要提交我们需要的文件。
- `git reset --hard <commit>`： **dangerous** 删除指定提交之后的所有本地修改（包括staged），重置为commit的状态

其中<commit> 可以用 `HEAD~n`快捷表示。 `HEAD~1`表示当前指针的前一个提交。

# git cherry-pick
- 拉取一个提交，并在当前分支添加一个新提交：`git cherry-pick <commit>`
- 拉取前可重新编辑提交信息：`git cherry-pick -edit <commit>`
- 拉取，但不作为commit提交，而是把变化加到stage：`git cherry-pick --no-commit <commit> `

# Ref 
- https://www.datacamp.com/tutorial/git-reset-revert-tutorial
- https://sentry.io/answers/list-all-files-in-a-git-commit/
- https://www.atlassian.com/git/tutorials/saving-changes/git-stash
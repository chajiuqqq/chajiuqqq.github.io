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

- git merge出现冲突时，不想处理冲突，取消合并：`git merge --abort`
- 如果已经merge了，要回退，用`git reset `或 `git revert`
- **误add了不想commit的文件到暂存区，怎么删除？** `git restore --staged <文件> `：将暂存区中的文件替换为仓库中某个提交的版本（默认为 HEAD）。这相当于取消暂存该文件。

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
- 查看某个stash的内容：`git stash show 0`
- 暂存指定文件：` git stash -m "ordSrv" -- oms/pkg/proto/orderservice_v3.go`

# git reset 
- `git reset --soft <commit>`：撤回提交并把变化存入stage区，不会丢弃本地文件的其他更改。
- `git reset --mixed <commit>`：这是git reset的默认参数。撤回提交，Unstage提交过的文件。这样我们只要提交我们需要的文件。
- `git reset --hard <commit>`： **dangerous** 删除指定提交之后的所有本地修改（包括staged），重置为commit的状态

其中<commit> 可以用 `HEAD~n`快捷表示。 `HEAD~1`表示当前指针的前一个提交。

> git reset和git revert的区别

**git reset：**

直接修改提交历史。当你在本地进行更改且还没有推送到远程仓库时，适合使用 git reset 来修改提交历史。
通常用于清理本地开发历史或撤销误提交。
使用：`git reset --hard HEAD~1`

**git revert：**

创建一个新的提交，用来“撤销”之前某个提交的更改。当某个提交已经被推送到远程仓库，且你不想破坏历史时，应使用 git revert 来“撤销”某个提交。
在协作环境中，推荐使用 git revert，因为它不会影响其他开发者的历史。

使用：`git revert <commit>`

# git cherry-pick
- 拉取一个提交，并在当前分支添加一个新提交：`git cherry-pick <commit>`
- 拉取前可重新编辑提交信息：`git cherry-pick -edit <commit>`
- 拉取，但不作为commit提交，而是把变化加到stage：`git cherry-pick --no-commit <commit> `

# git push
- 推送本地分支到远程，并创建追踪：`git push -u origin <branch-name>`，此时远程分支名称会和本地一样。`-u` 是` --set-upstream`的简写。
-  可以手动指定远程的分支名称：`git push -u origin branchB:branchB`

# git restore
用于将暂存区/某个提交的版本覆盖到工作区/暂存区。

**误add了不想commit的文件到暂存区，怎么删除？**

`git restore --staged <文件> `：将暂存区中的文件替换为仓库中某个提交的版本（默认为 HEAD）。这相当于取消暂存该文件。

- **从暂存区恢复到工作目录**: git restore <文件> 会将工作目录中的文件替换为暂存区中的版本。如果暂存区中没有该文件，则会从工作目录中删除该文件。
- **从仓库中的某个提交恢复到工作目录**: git restore --source=<提交> <文件> 会将工作目录中的文件替换为指定提交中的版本。
- **从仓库中的某个提交恢复到暂存区**: git restore --source=<提交> --staged <文件> 会将暂存区中的文件替换为指定提交中的版本。
- **从仓库中的某个提交恢复到工作目录和暂存区**: git restore --source=<提交> --staged --worktree <文件> 会同时将工作目录和暂存区中的文件替换为指定提交中的版本。

> **也就是可以用选项指定源头和目标：**
> `--source=<提交>`: 指定恢复文件的来源提交。可以是提交哈希值、分支名或标签名。默认为 HEAD。
> `--staged`: 指定恢复目标为暂存区。
> `--worktree`: 指定恢复目标为工作目录。

---
**HEAD~2和HEAD^2有什么区别**
HEAD~2用于向上追溯找到前2个提交
HEAD^2用于找到第二个父提交（HEAD必须是merge节点）
![image](https://github.com/user-attachments/assets/7d993140-155b-4b8d-8d50-742d386a92fa)

HEAD^ 和 HEAD~ 在大多数情况下指向同一个提交


# Ref 
- https://www.datacamp.com/tutorial/git-reset-revert-tutorial
- https://sentry.io/answers/list-all-files-in-a-git-commit/
- https://www.atlassian.com/git/tutorials/saving-changes/git-stash
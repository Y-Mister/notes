### GIT基本操作



##### 如何进入命令行管理

到达目标文件夹，右键打开git bash或者按住shift+右键使用在本文件打开powershell进入git命令行管理

在目标文件夹下（F:/GitCode）下，每一个子文件夹为一个工作区，进入一个工作区，可见.git文件夹，里面保存的是当前文件夹在git管理下的相关信息

##### 相关命令

| 命令                                             | 作用                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| git clone SSHKey                                 | 从GitHub上你想要down下来的repository复制SSH KEY，然后通过此命令下载你需要的reository |
| git config --global user.email "you@example.com" | 设置你提交是使用的email                                      |
| git config --global user.name "Your Name"        | 设置你提交是使用的名称                                       |
| git status                                       | 查看当前工作区的状态                                         |
| git add 文件名.后缀                              | 将文件内容添加到索引(将修改添加到暂存区)，可使用通配符       |
| git commit -m "注释信息"                         | 将add的修改添加到存储库中                                    |
| git push                                         | 将本地分支的更新，推送到远程主机(从github上下载，则将修改提交到github) |
| git pull                                         | 取回远程主机某个分支的更新，再与本地的指定分支合并，它的完整格式稍稍有点复杂; 将当前工作区与远程工作区的文件合并 |
| git checkout                                     | 恢复工作树文件，重写工作区                                   |
| git log                                          | 查看日志                                                     |
| git show commitID                                | 查看某一次commit的详细情况，ID在git log中会有                |
| git branch branchName                            | 列出、创建或删除分支                                         |
| git checkout branchName                          | 切换到指定的分支                                             |
| git checkout -b branchName                       | git branch branchName与git checkout branchName的合并         |
| git merge branchName                             | 分支合并                                                     |
| git merge origin/branchName                      | 当一个branch由他人创建时，通过次命令远程拉取合并             |
|                                                  |                                                              |

##### git通配符
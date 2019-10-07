# Git操作



### 命令积累

| 命令                                            | 操作                                  | 说明                                                         |
| ----------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ |
| git remote -v                                   | 查看当前远端地址                      |                                                              |
| git remote set-url origin {remoteUrl}           | 修改当前远端地址                      | 可以修改为SSH或者HTTP(S)                                     |
| git rm -r --cached {targetFile/targetDirectory} | 删除本地文件缓存（改变成未track状态） | 解决问题：某些文件已经被提交到远端，但是这些文件是不应该被提交的（如target目录），使用该命令后，重新commit and push即可 |
| git config --global http.postBuffer 524288000   | 配置缓存区大小                        | 大约为500MB<br />解决问题：RPC failed; curl 18 transfer closed with outstanding read data remaining |
|                                                 |                                       |                                                              |



### 问题记录

* 本地和远端有不相关的历史
  * git fetch
  * git merge
  * git commit
  * git pull
  * git push
  * 原理：先将远端的修改获取到，然后本地合并（可能存在需要修改的内容，提交前过一遍），最后提交
* 
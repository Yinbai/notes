# Git



### 合并



### 回滚



### 标签

`git tag` 查询本地标签列表

`git ls-remote --tags` 查询服务器标签列表

`git tag 标签名` 新增本地标签

`git tag -d 标签名` 删除本地标签

`git push origin 标签名` 向服务器推送指定标签 

`git push origin :refs/tags/标签名` 删除服务器上的指定标签



### Workflow



### 时光穿梭

场景：commit feature 1-6，然后执行强制回滚，丢失了 commit 1-6，又新增了 commit feature 7，此时需要找回 1-6，并保存 7

`git reflog` 展示所有的 git 操作，记录下 commit feature 6、7 的 hash 码

`git reset —hard [6 hash]` 回滚到 commit feature 6

`git cherry-pick [7 hash]` 找回 commit feature 7
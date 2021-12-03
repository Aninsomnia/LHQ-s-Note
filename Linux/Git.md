# 删除本地/远程分支

https://chinese.freecodecamp.org/news/how-to-delete-a-git-branch-both-locally-and-remotely/

# 关联多个远程仓库

https://zhuanlan.zhihu.com/p/82388563

# pull/fetch的区别、真正理解pull/fetch

### fetch

* ```shell
  git fetch
  ```

  更新**所有远程仓库**包含的分支的最新commit-id, 将其记录到.git/FETCH_HEAD文件中

* ```shell
  git fetch remote_repo
  ```

  更新**名为remote_repo 的远程仓库**上的**所有分支**的最新commit-id，将其记录。 

* ```shell
  git fetch remote_repo remote_branch_name
  ```

  更新**名为remote_repo的远程仓库**上的**remote_branch_name分支**

* ```shell
  git fetch remote_repo remote_branch_name:local_branch_name
  ```

  更新名称为remote_repo 的远程repo上的分支： remote_branch_name ，并在本地创建local_branch_name 本地分支保存远端分支的所有数据。

### pull

* ```shell
  git pull
  ```

  

https://segmentfault.com/a/1190000017030384

https://blog.csdn.net/QH_JAVA/article/details/77969010

https://blog.csdn.net/weixin_41975655/article/details/82887273

# git branch(分支)

### 查看上游分支

* ```shell
  git status
  ```

### 设置上游分支

* ```shell
  git branch --set-upstream-to=origin/<远程分支> <本地分支
  ```

### 取消分支上游：

* ```shell
  git branch --unset-upstream
  ```

# git push

### 遇到冲突

* 使用reset命令进行版本退回：

  ```shell
  git reset --hard head
  ```

# git checkout

### 创建空白分支

* 使用传统的git checkout命令创建的新分支是**有父节点**的，意味着**新分支包含了历史提交。**

* 使用 `git checkout`的**`--orphan`**参数:

  ```shell
  git checkout --orphan new_empty_branch
  ```

  生成一个名为`new_empty_branch`的空白分支，该分支会**包含父分支的所有文件**，但**不会指向任何以前的提交**，即没有任何历史。如果此时提交当前内容，那么这次提交就是这个分支的首次提交。

* 想要空白分支，需要**把当前内容全部删除**：

  ```shell
  git rm -rf . 
  ```

* 如果**没有任何文件提交的话，分支是不可见的**，所以需要创建一个新文件，然后提交新创建的文件，该空白分支就会显示出来：

  ```shell
  echo '# new branch' >> README.md
  
  git add README.md
  
  git commit -m 'new branch'
  ```

# git更新本地分支的方法

### 方法一：使用git pull 更新

* ```shell
  git checkout local_branch_name
  git pull remote_repo remote_branch_name
  ```

  这将远程remote_repo仓库的remote_branch_name分支合并到本地的local_branch_name分支

Todo：https://blog.csdn.net/QH_JAVA/article/details/77969010

更新远程文件到本地方式一
查看远程仓库
git remote -v
从远程获取最新版本到本地
git fetch origin aaa
比较远程分支和本地分支
 git log -p aaa origin/aaa
合并远程分支到本地
git merge origin/aaa
远程文件到本地方式二，在本地建临时分支，合并后删除
查看远程仓库
git remote -v
从远程获取最新版本到本地
git fetch origin master:temp
比较远程分支和本地分支的区别
git diff temp
合并远程分支到本地
git merge temp




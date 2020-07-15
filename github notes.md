# Github

### 建立对应的本地仓库

* 流程：

1. 在github上建立一个新的repo
2. 使用 `git clone` 在本地建立仓库

* 注意
  * 克隆得到的repo只有master分支



### 提交：改动多个文件后同步到远程仓库上

* 查看本地分支上的文件变动

```bash
git status	#用于查看有哪些文件可以提交：标红
            #分为修改的文件 和 untracked（新的） 文件
```

* 原子化提交：

```bash
git add <file>	#只添加/stage一个文件
git add <file> <dir> ...	#可以添加多个文件和文件夹（自动识别文件夹下变动的文件）
git commit -m "message of this add"	#给add添加的文件打包成一次commit，并贴上commit信息
                                    #commit信息会出现在github网站上，作为文件的最新说明
                                    #所有被打包的文件改动，都显示相同的commit信息
```

* 更新到远程仓库

```bash
git push <remote> <local-branch>	#将分支上已有的commit同步到远程仓库的相应分支
                                    #输入github账号密码
# 如果local-branch是在本地建立的新分支，可以直接push，会在远程仓库中建立对应的分支
```

不需要切换到想要push的分支，可以在别的分支上完成push

* .gitignore文件

使用通配符来限定哪些文件不会被上传，[与linux通配符略有不同](https://www.atlassian.com/git/tutorials/saving-changes/gitignore)

```bash
$ cat .gitignore	#内容例子
*.pyc
**/.ipynb_checkpoints
*.pkl
!train_vocab.pkl 
*.ipynb
**/tmp
$ echo *.pt >> .gitignore	#添加新的规则
```

本地分支上添加/更新 .gitignore文件之后，`git status` 和 `git add .` 会马上应用，前者看不到被屏蔽的改动文件，后者不会将被屏蔽的文件打包。

* 注意
  1. 如果add 添加的文件，是远程仓库已有的文件（tracked文件），则git commit会检查文件内容是否有改动，如果没有改动，commit是无效的：git push不会修改远程仓库的文件，也不会改commit信息；
  2. .gitignore文件创建或改动后也需要同步到远程仓库；
  3. 本地变动的文件即使没有被屏蔽，也不一定需要同步到远程仓库



### 分支操作

* 查看分支

```bash
git branch	#查看现在的分支和本地上可以直接切换的分支
git branch -a	#查看所有分支，包括远程仓库有的分支（根据本地.git中的remote信息）
git fetch <remote> <new-branch>	#更新本地的remote信息，使得在github网站上创建的新分支，能够用                                  git branch -a 查到
```

* 切换分支：本地已有的分支切换

```bash
git checkout <remote> <branch>	#如果只有origin一个remote，则可以不写<remote>
```

* 创建新本地分支

```bash
git checkout -b <new-branch> --track <remote>/<branch>	#本地创建分支，并指定track的目标
git checkout <new-branch>	#本地只有一个remote时，快捷创建分支，并自动指向remote的同名分支
```

* 更新master分支

一般是合作者在fork分支上；无需输入账号密码

```bash
git pull <remote> master	
```

之后会进入.git/MERGE_MSG 的编辑器中，已有一句默认merge message，可以直接按 ctrl+X（大写） 退出完成pull



### 其他命令和问题解决

* 查看本地仓库添加的远程仓库链接

```bash
git remote -v
```

* 记忆默认的remote branch，简化命令

```bash
git push -u <remote> <branch>	#之后只要执行git push即可自动推（没试过）
```

* 解决因上一次pull失败而导致的 “error: You have not concluded your merge (MERGE_HEAD exists)”

```bash
git merge --abort
git pull <remote> <branch>
```

abort上一次未完成的pull，重新来过

* `git archive` : vreate distributable packages of git repositories

* `git init`：创建新的空repo；将已有project转化为repo；创建central repo（`--bare`）；创建包含特定文件和文件夹（比如README.md或者hooks）的新repo（`--template`）

* git hooks：维护项目者设置的，执行Git 操作时如果存在违反会触发警告，比如对commit message的格式规范等

* `git clone` 相关

  * `git clone -depth=1 <repo>`：对于历史较长的repo，这样做得到的克隆repo只有central repo的最近一次commit，是很经济的做法。其他不受影响。
  * `git clone -branch <remote-new_feature> <repo>`：直接复制远程repo上的某一个branch，而不需要先clone master分支之后，再fetch 想要的分支

* `git config`：设置名称、邮箱地址、各种颜色显示、editor、自定义命令alias、检测空白行、各种调用的MSG编辑器

  * 修改commit/merge等message编辑器：`git config --global core.editor "vim"`

* `git alias`：通过修改config文件内容或者使用`git config` 进行自定义命令

* `git commit` 相关

  * `git commit -am "commit message"`：将work dir上所有staged过的文件全部提交，没有add过的不会自动提交
  * `git commit --amend -m "commit message"`：将新的staged changes结合到上一个commit中的changes，对上一个commit进行修改
    1. 如果没有新的staged changes，则相当于修改上一个commit 的message；
    2. 如果有新的追加staged changes，建议不通过`-m` 输入commit message，而是进入editor进行补充。
    3. 如果只是比如忘了加上某个文件，那么可以用`--no-edit` 保持原来的commit 信息
  * 进入text editor写 commit message的好处是能够具体说明，格式参照下面，在`git log` 中能显示具体的message

  ```
  e.g.:
  Change the message displayed by hello.py
  
  - Update the sayHello() function to output the user's name
  - Change the sayGoodbye() function to a friendlier message
  
  # Please enter the commit message for your changes. Lines starting
  # with '#' will be ignored, and an empty message aborts the commit.
  # On branch master
  # Changes to be committed:
  # (use "git reset HEAD ..." to unstage)
  #
  #modified: hello.py
  ```

* `git diff` 

  * **功能**：展示工作路径上未commit（默认相对于最近一次commit）的变化。
  * **内容解读**
  
  ```bash
  $ git diff
  diff --git a/diff_test.txt b/diff_test.txt # diff 文件来源
  index 6b0c6cf..b37e70a 100644 # meta数据
  --- a/diff_test.txt # 文件marker
  +++ b/diff_test.txt
  @@ -1 +1 @@ # header，"-m,n"表示被“-”标记的文件（第一个文件）在第m行开始的下面n行，不是删减的意               # 思！
  -this is a git diff test example # 改变的内容
  +this is a diff example
  ```
  
  * **二进制文件的比较**：需要指定converter 程序将二进制文件转化为文本，才能进行比较。可以比较的二进制文件比如 pdf, doc, zip等。
  * **具体文件的变化比较**：
  
  ```bash
  git diff /path/to/file # 显示没有staged的变化，默认是工作路径上的文件跟HEAD中的文件进行比较
  
  # 如果 /path/to/file 已经进入staged area，则需要加上--cached
  git diff --cached /path/to/file # 比较staged changes与local repo的差异
  
  # 如果/path/to/file的改变已经commit，则git diff显示空
  ```
  
  * **其他**：还可以比较 历史中不同commit 或 不同分支 的不同
  
  ```bash
  # 查看两个时间点发生的具体文件变动
  git diff <commit1 SHA-1> <commit2 SHA-1> # 这两个时间点的snapshot变动，SHA-1只需要前5或6个                                          # 字符
  
  # 查看某一个commit的文件变动
  git diff <commit SHA-1> <commit SHA-1>~1 # 跟前一个时间点比较，就能知道该时间点的具体变动
  
  # branch 之间的diff
  git diff <branch1>..<branch2> # 两个分支的头部的差异
  git diff <branch1>...<branch2> # 两个分支的分叉点，与branch2的头部，之间的差异
  ```
  
  
  
* `git stash`：将还没有commit的改动隐藏储存起来，可以切换到改动前的状态**进行测试和其他commit**，然后再重现之前被隐藏的改动。

* `git log` 命令

```bash
git log <branch name> # 展示（当前分支HEAD的）完整历史，空格键下拉，q退出
git log -n <limit;int> # 只显示若干条commit历史
git log --oneline # 每条commit记录显示为一行
git log --stat # 额外展示每条commit中被改动的文件，及其被增删的行数
git log -p # 额外显示每条commit的diff信息（最详细）
git log --author="<pattern>" --grep="<pattern>" # 用字符串或者正则表达式搜索相关作者或者commit                                                   # 信息的commit
git log <since ref|branch1>..<until ref|branch2> # 显示某个commit区间历史，或者相对branch1，                                                    # branch2 独有的commit历史
git log <file> # 只显示包含该文件变动的commit

## 类似git blame的功能：查找某个文件的某一行的改动历史，并以制定的形式显示结果
git log -S "<a line from a file>" --pretty-format='<pattern>'
# 存疑：不知道怎么解决同一句出现在不同文件内的冲突；
#      不知道怎样的改动才会被记录下来：如果查找的句子被改动过现在已经不存在，那么查不到历史；行号的变化也不会被记录；同一行内的其他内容发生变化也不会被记录
```

* `git tag`

  * **比较lightweight tag vs. annotated tag**：包含的meta数据不同。前者只是一个commit的书签，常作为非公开用途，后者还包含了邮箱、创建日期、tag message等，还可以进行安全性地数字签字和验证，常作为public release。
  * **命令**：

  ```bash
  git tag # 查看已有的tag
  git tag -l *-rc* # 搜索符合正则表达式的tag
  git tag v1.0 # 给当前的repo状态创建lightweight tag
  git tag v1.0 15027957951b64cf874c3557a0f3547bd83b3ff6 # 给某一个历史状态创建lightweight                                                         # tag
  git tag -a v1.0 -m "<tag message>" # 创建annotated tag，加上tag message
                                     # 非当前状态的tag与lw tag用法一样
  git tag v1.0 -f # retag或者替换tag
  git push origin v1.0 # 需要显式地推送到远程仓库；推送多个tag使用 --tags
  git checkout v1.0 # checkout
  git tag -d v1.0 # 删除
  ```

  * **其他**：
    * 两者均不会创建新的commit；annotated tag会给被标记的commit打上tag的标识，可以由`git log` 查看。
    * tag中的 “rc” 意思是 release candidate
  
* `git blame`：用于呈现某个文件每一行的最后一个改动者的信息（姓名、时间、邮箱等）。

* 撤销改变的做法

  * `git checkout`：[**适用于非master分支**] 使用checkout 回到之前的某个时间点上，然后使用`git checkout -b <new-branch>` 建立新的时间线。这种做法默认之前的时间线是不用的了。
  * `git revert` ：[**不在意宛转的时间线以及远程仓库是共享的**] 
  * `git reset --hard <commit SHA-1>`：[**适用于本地或者私人repo**] 回到指定的时间点，并且还原之后工作路径上的所有改动。最干净的做法，但是之后向远程仓库push会有“not up to date”的错误，只能不push或者连同远程仓库一起改。
  * `git commit --amend`：[**修改少数几处就能解决**] 详见`git commit` 部分
  * `git clean`：[**工作路径上的untracked文件**] 清除没有被追踪的文件
  * `git reset`：[**主要用于还原staging index或工作路径上的变动**] 
  
* `git revert`

  * 做法：创建一个新的commit，用于撤销指定的commit 的变动，而后续的变动维持不变。如果撤销该commit的变动，会与保留之后的commit有冲突，则revert报错。
  * 好处：安全操作；仍然能够继续在这个branch上进行工作，而且后续的push也不会发生“not up to date”的错误；能够实现历史中的某一个commit的改动被还原，而后续的commit不变（如果不冲突的话）【需要验证】
  * 命令
  
  ```bash
  git revert --no-edit <commit ref/SHA-1> # 自动填写message，格式为 Revert "<被撤销的commit 的message>"
  
  git revert -n <commit ref/SHA-1> # 不新增commit，只同时改动staging index和工作路径。
  git revert --abort # 结束因为冲突而中断的上一次revert
  ```
  
  
  
* `git reset`

  * 用法：**仅适用于那些还没有push/推到远程仓库的commits之间的回溯和还原**

  ```bash
  git reset file # 仅将该文件从staged area还原到工作路径上（？）
  git reset --mixed HEAD # git reset的默认参数

  git reset --hard <commit SHA-1> # 回到指定的时间点，删除历史，并且还原之后的staging index和工作路径上的所有改动。最干净的做法，但是如果向远程仓库push会有“not up to date”的错误，只能不push或者连同远程仓库一起改。
  ```
  
  * 说明
    * reset的做法是将HEAD ref 和branch ref 同时指向指定的时间点；而checkout 只是改变HEAD ref
    * reset会产生 orphaned commit，Git 每30天会回收和永久删除这些脱离的commit，在此之前都保留在`git reflog` 中
    * HEAD作为输入，不会改动commit 历史，只会重置staging index 和工作路径
  
* `git ls-files -s`：查看在staging index中的文件

* `git rm`：停止追踪某个文件（夹）。作用等于shell的rm + `git add`，那些已经被tracked的文件，删除一定要使用`git rm` 。

  * 其他
    * 如果只用了shell的rm命令删除文件，还是有补救的方法
    * 允许使用通配符匹配要删除的文件

* 改写历史commit：别改public commit

  * 修改上一次的commit：`git commit --amend`，修改原来的，不新增
  * 修改更久之前的commit：修改完后用 `git rebase` 自动应用后续的commit

* `git reflog` 查看头部ref的变动历史







### 特殊operator

* `ref~n`：相对时间点的表示方法，比如`HEAD~2` 表示HEAD的前前一个时间点
* 







### 团队合作流程（workflow）

* 项目发起者完善项目

  1. 创建repo，并完成对master 分支的第一次push（提交多个commit，可能是添加占位文件来确定repo的框架）之后，需要建立 develop 分支；

  2. 本地建立 develop分支，并在该分支上完成下一次push之前的多个commit；

  3. 本地在develop分支的提交推到origin/develop后，发起pr，方便统一地检查改动；origin/develop 的角色类似 fork出去的分支；

  4. 注意：

     （1）在master上完成第一次push之后，最好不要再在master分支上提交；

     （2）develop分支的好处是：方便检查改动 和 发起的pr可以要求合作者review

  5. 用于搭建新的feature或者debug，创建的分支最好别用 develop命名，而是更有指向性的；保留develop 用于完善不完整的项目的用途。

* 合作者contribute

  1. fork 项目发起者的master分支
  2. 跟发起者的develop分支做的事情类似，确认改动后发起pr

* 其他

  1. 合作者之间要分工明确，不要同时对同一个文件进行改动（模块化的要求）



### 注意

1. 不要轻易删掉本地的repo，里面有一些本地文件并没有上传但却是有用的，需要保留。



### Github理解

1. `git fetch` + `git merge`  ~= `git pull`

2. merge的逻辑

   （1）将改动后的分支A'上新增的commits，在被merge的分支A上再执行一遍；

   （2）一般分支A'是从分子A分出去。如果在分支A' merge到分支A之前，分支A增加（并应用）了一些新的commits，如果这些commits 跟 分支A' merge到分支A时需要执行的commits没有冲突，比如两个分支改动的文件是完全不同的，那么merge可以正常发生
   
3. staging area是实现多个不相关的修改，片段化/原子化封装和commit的缓冲区。（git add）staging area是working directory和project history之间的缓冲，（git commit）local repo是contributions 和 central repo之间的缓冲。

4. branch和commit

   （1）`git commit` 是保存某一个时刻的整个repo的snapshot，而不是difference，也就是记录的是文件的完整内容

   （2）branch可以认为是时间线，而commit是时间线上milestone。

5. **Git ref**：references for SHA-1 values of commits，即给SHA-1值的别名/标签/替代名，方便指代。

   * normal refs vs. symbolic ref：前者指向commit的SHA-1值，后者指向一个normal ref

   ```bash
   $ cat .git/refs/heads/develop2 #normal ref
   93e4ab7ebffee90b703deaa43833430f58c586d2
   $ cat .git/HEAD #symbolic ref
   ref: refs/heads/develop2
   ```

   * `.git/refs` 内包含

     * `heads/`：（1）head of a line of work，包含了机器上能直接切换的branch，每个branch/头部文件里面SHA-1 值，指向的是该分支的历史的最近一次commit / 头部，发生`git commit` 会改变对应branch头部文件的内容。（2）这里包含了所有可选的头部，而实际的头部是由`.git/HEAD` 这个symbolic ref 的内容确定的，`git checkout <branch>` 会改变`.git/HEAD` 的内容，从而实际的头部指向制定的头部。
     * `tags/`：给commit打上固定的标签，但不像heads，标签内的内容不会发生改变；版本发布时用到。还可以给其他 git object打标签。

     ```bash
     # 创建lightweight tag
     git update-ref refs/tags/v1.0 cac0cab538b970a37ea1e769cbbde608743bc96d
     git tag v1.0 cac0cab538b970a37ea1e769cbbde608743bc96d # 等价操作
     
     # 创建annotated tag:创建refs/tags/v1.1，里面包含的内容是另一个指向
     #                  1a410efbd13591db07496601ebc7a059dd55cfe9 的 SHA-1值
     git tag -a v1.1 1a410efbd13591db07496601ebc7a059dd55cfe9 -m 'Test tag'
     ```

     * `remotes/`：储存 远程repo的头部SHA-1，发生 push时，会改变其中的内容。跟heads不同的地方在于只读，即可以checkout到该头部，但是不能commit来改变该remote分支的头部ref。

   * 其他

     * 除了checkout，另一种切换branch的方法：

     ```bash
     git symbolic-ref HEAD refs/heads/test # 需要在.git/路径下执行
     ```

6. Git’s three main object types：*blobs*, *trees* and *commits*

7. git 示意图中的箭头的含义

8. repo几个重要的构成元素

   （1）working directory是repo中的元素，它会与本地的文件系统实时同步：所以切换branch时，看到的本地文件是不一样的，可能其实显示的是repo的工作dir

   （2）staging index：commit缓冲区



### 参考链接

1. https://www.atlassian.com/git/tutorials：简短易懂，有命令演示
2. https://www.atlassian.com/git



# temp

0608进度：https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-tag
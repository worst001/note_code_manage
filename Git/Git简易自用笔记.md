# Git简易自用笔记

-------------------
## 参考资料

[尚硅谷_Git教程](./git.pdf)

---
## 基本知识

### 1、相关工具

#### gitweb
    - fcgiwrap
      [https://blog.twofei.com/642/](https://blog.twofei.com/642/)
        - 使无法使用CGI的服务器可以跑CGI
    - spawn-fcgi
        - 平滑创建fcgiwrap的socket
    - repository
        - 仓库根目录
          $projectroot = "/home/git/repositories";
    - 权限
        - 不能比调用者进程权限高
          clown git:nginx fcgiwrap.socket
    - socket
        - unix:socket
#### gitosis
    - writable
#### github
#### gitlab

### 2、protocol
+ local
+ http
+ ssh
+ git

### 3、常用hook
+ post-receive 服务端响应
+ pre-commit 客户端响应

### 4、常用配置Config
+ --global, --system 配置作用域
+ user 用户配置
+ core.editor 编辑器
+ list 列出当前配置
+ core.quotepath  显示中文
+ core.autocrlf 换行符格式
+ `git config --global core.safecrlf false`:是否保护crlf格式的文件

### 5、Attributes
+ merge=ours 本地优先
+ export-ignore 忽略归档

### 6、分支 branch
+ git branch 新的分支
+ merge 合并分支
+ conflict 冲突
+ rebase 变基
+ upstream 与远程同步

### 7、提交 Commit
+ log 日志
+ GPG tag 标签加密
+ revert 撤消
+ amend 补充
+ stash 贮藏未提交代码

### 8、追踪 Track
+ add cached 追踪文件
+ status 仓库状态
+ diff 与已追踪文件比较
+ checkout 定位到某快照
+ reset 重置快照

### 9、仓库 Repository
+ init 初始化
+ remote&fetch 远程映射与抓取
+ clone 克隆
+ pull&push 下拉与推送


### 10、标签 Git Tag
+ 用来发布某个版本
+ 指针指向某个快照
+ 可以认为是个别名


---
## git目录下object文件过大清理

```bash
# 查看最大的 3 个文件
git verify-pack -v .git/objects/pack/pack-*.idx | sort -k 3 -n | tail -3

# 查询大文件的真实文件名与路径
git rev-list --objects --all | grep 829b9d04b876f058b56849f7296c270a12f3ce04

# 将该文件从历史记录的所有 tree 中移除
git filter-branch --index-filter 'git rm --cached --ignore-unmatch  dist.rar'

# 重新构建 objects
rm -rf .git/refs/original/

git reflog expire --expire=now --all

git fsck --full --unreachable

git repack -A -d

git gc --aggressive --prune=now

# 最后上传到远程
git push --force

```


##  Git 分支规范

### 分支表
| 分支    | 命名规范    | 示例                  | 备注                                                                |
|---------|-------------|-----------------------|---------------------------------------------------------------------|
| master  | master      | master                | 主分支 - 上线时用该分支打版                                         |
| develop | develop     | develop               | 开发分支 - 项目分支合并到该分支进行测试, jenkins根据该分支 自动部署 |
| release | release-*** | release-20220714v1.0  | 上线分支 - 项目复杂时可选, 用 master 打版时创建                     |
| feature | feature-*** | feature-some-business | 项目分支 - 必须从 master 中创建项目代码为主分支下最新               |
| hotfix  | hotfix-***  | hotfix-some-bug       | 热修复分支 - 用来修复线上 bug 的分支, 必须从 master 中创建          |

### 补充说明与建议
1. release 是β测试版本 即灰度版本, 最接近生产
2. 各个分支改动建议
+ master
    + 分支不能做任何 commit 操作
    + 必要情况下只可修改 pom 文件保证依赖包为正式环境
+ develop
    + 尽量不做任何 commit 操作
    + 必要情况下只可修改 .properties 和增加日志打印操作
    + 遇到需要频繁打版测试时可在本地打版发布
    + 也可使用远程 debug 调试
    + 调试通过后用 stash/unstash 操作将代码转到 feature 上
+ feature
    + 确认 develop 通过测试人员测试后才可将 feature 合并到 master
+ hotfix
    + 修复 bug 后也需要通过合并到 develop 分支测试
+ tag
    + 稳定版打上标签作为正式上线版本

# Git整理

+ Git思维导图
<!-- open /Users/hanwenhao/Cloud/思维导图/Git.mindnode -->

+ 公司git环境
<!-- open http://git.xiyouwangluo.top/ -->

+ 一些有用的配置
`git config --global core.safecrlf false`:是否保护crlf格式的文件

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

## Git Tag
指针指向某个快照
可以认为是个别名


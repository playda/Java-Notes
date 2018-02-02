1. git add 文件名（把文件提交到暂存区）
2. git commit -m '修改信息'（把文件提交到仓库）
3. git remote add origin 远程库地址（关联到远程库，无需重复）
4. git pull --rebase origin master（获取远程库与本地同步合并，不必要，但如果远程库含有本地未知的文件则必须做这一步）
5. git push（把本地库的内容推送到远程，实际上是把当前分支master推送到远程）

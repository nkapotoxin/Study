# 1  git tag operations

## 1.1  创建标签
基于当前HEAD 指向的commit id 创建一个名称为v1.0的tag

	git tag v1.0

基于commitid创建tag

	git tag v1.0 <commitid>

带注解的tag

	git tag -a v1.0 -m "标签注解" <commitid>

## 1.2  查看标签
显示所有标签

	git tag

查看tag相关的提交信息

	git show <tag_name>

## 1.3  删除标签
	git tag -d <tag_name>

## 1.4  检出标签
	git checkout -b <new_branch> <tag_name>

## 1.5  推送标签
所有标签推送至远程

	git push <remote> --tags 

指定标签推送至远程

	git push <remote> <tag_name>

## 1.6 回退代码到某个commitid

    git reset --hard commitid



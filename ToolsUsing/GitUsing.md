# GIT

#### 1.更改本地默认仓库

cd 你的目的地址

git init

#### 2.clone仓库到本地

git clone 仓库地址，如

git clone https://gitee.com/user/demo.git

#### 3.添加远程仓库

git remote add origin https://github.com/user/demo.git

#### 4.删除默认关联的远程仓库

默认的仓库名为origin

git remote rm origin

#### 5.提交本地项目到远程仓库

git add .   注： 将本地目录下所有文件选中

git commit  -m  "提交信息log" 注：“提交信息”里面换成你需要，如“first commit”

git push -u origin master  注：此操作目的是把本地仓库push到github上面,如果已经设置了公钥直接git push就可以

#### 6.创建分支

git checkout -b branch_name，创建新的分支branch_name

git add -A，将项目下文件添加

git commit -m "提交信息"

git push origin branch_name，提交到远程仓库


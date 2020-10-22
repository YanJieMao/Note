# Git基本操作

## Git配置

查看配置 git config -l

<img src="img/image-20200720170109557.png" alt="image-20200720170109557"  />



查看不同级别的配置文件

```
#查看系统config
git config --system --list

#查看当前用户（global）配置
git config --global --list

```



**Git相关的配置文件：**

1）、Git\etc\gitconfig  ：Git 安装目录下的 gitconfig   --system 系统级

2）、C:\Users\Administrator\ .gitconfig   只适用于当前登录用户的配置  --global 全局



> 设置用户名与邮箱（用户标识，必要）



安装git后最重要的就是设置用户名和Email地址

每次提交都会用到这些信息

```
git config --global user.name "YanJieMao" 
git config --global user.email "YanJieMao@qq.com"
```



*如果你希望在一个特定的项目中使用不同的名称或e-mail地址，你可以在该项目中运行该命令而不要--global选项。总之--global为全局配置，不加为某个项目的特定配置。*



## Git命令

### 新建仓库

> 在本地新建 git init 

<img src="img/image-20200720192525531.png" alt="image-20200720192525531" style="zoom:80%;" />

> 从远程仓库clone git clone https://github.com/mkdirHao/git_test.git

<img src="img/image-20200720192815294.png" alt="image-20200720192815294" style="zoom:80%;" />



### 添加到缓冲区

> git add

<img src="img/image-20200720193141314.png" alt="image-20200720193141314" style="zoom:80%;" />



### 提交到本地仓库

> git commit

<img src="img/image-20200720193354940.png" alt="image-20200720193354940"  />



### 提交到远程仓库

> git push



<img src="img/image-20200720193812421.png" alt="image-20200720193812421" style="zoom:80%;" />

输入命令后会让输入账户密码

<img src="img/image-20200720193858286.png" alt="image-20200720193858286" style="zoom:50%;" />

 账号密码正确后就可以成功提交到远程仓库了

<img src="img/image-20200720194133682.png" alt="image-20200720194133682" style="zoom:80%;" />

看一下我们的远程仓库，这时已经有了

<img src="img/image-20200720194356327.png" alt="image-20200720194356327" style="zoom:80%;" />




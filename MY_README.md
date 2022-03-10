## 项目说明

### 一、项目结构

* saleor-platform
  * saleor
  * storefront
  * dashboard


### 二、部署说明
1. 目前项目部署在本地电脑，内网穿透实现

2. 安装依赖时需要挂代理操作，所以 Dockerfile 以 Dockerfile.dbg 为主；同理，安装 docker-compose build 也需要指定环境 （定义为测试环境）

```shell
docker-compose build -f ./docker-compose-dbg.yml
```

3. 后续如果上美国服务器，再用 Dockerfile



### 三、关于 Git 的分支管理
> 因为项目是通过 git 的 submodules 管理，方便了部署，但是增加了一定维护的复杂度

1. saleor-platform 上的分支直接正常操作提交即可
2. saleor-platform 的子分支
   -  在子分支里面提交
```git
git add xxx
git commit `子分支的一个修改`
```


   -  然后切换到父分支提交一个子分支的变更，如下
```git
git add storefront
git commit `在父分支记录一个子分支修改`
```

3. 在远程update 刚才的更改，先切换到父分支

```
git pull
git update submodules
```

### 四、项目启动说明


1. Clone the repository:

```
$ git clone https://github.com/williamyorkl/saleor-platform.git --recursive --jobs 3
```

2. Go to the cloned directory:
```
$ cd saleor-platform
```

3. 切换到 feature/vX.X.X (如 feature/v3.1.1)
```
$ git checkout feature/v3.1.1
```

4. Build the api application (后端服务)
> 本地服务器要指定 dbg
```
$ docker-compose -f docker-compose-dbg.yml build api
```

5. Apply Django migrations:
```
$ docker-compose run --rm api python3 manage.py migrate
```

6. Collect static files:
```
$ docker-compose run --rm api python3 manage.py collectstatic --noinput
```

7. Populate the database with example data and create the admin user:
```
$ docker-compose run --rm api python3 manage.py populatedb --createsuperuser
```
*Note that `--createsuperuser` argument creates an admin account for `admin@example.com` with the password set to `admin`.*

8. Run the application (再起前台服务)
```
$ docker-compose -f docker-compose-dbg.yml up
```
*Both storefront and dashboard are quite big frontend projects and it might take up to few minutes for them to compile depending on your CPU. If nothing shows up on port 3000 or 9000 wait until `Compiled successfully` shows in the console output.*
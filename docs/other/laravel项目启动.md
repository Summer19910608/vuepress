---

title: Laravel启动项目

meta:

  - name: description

    content: 博客文章

  - name: keywords

    content: 博客，文章

date: 2019-12-09

update_date: 2019-12-09
 
tags: 
  - Git
  - Dokcer

  - Yur

  - Blog
  - Mysql
  - Laravel
---





###### 把一个 laravel 下载下来后目录结构基本都这样；

![image](https://baijunyao.com/uploads/article/20180415/5ad36a00d320b.jpg)

###### 2. 刚下载下来的 laravel 跟正常使用的相比差个 .env 文件和 vendor 文件夹 ；
我们需要复制跟目录下的 .env.example 文件并重命名为 .env ；
这个是项目的示例配置文件；
我们需要改成自己的实际配置；

![image](https://baijunyao.com/uploads/article/20180415/5ad36a4f4f819.jpg)


###### 3.APP_NAME 就是自己的项目名称比如我的白俊遥博客；
APP_URL 就是我们的项目链接比如说我的 https://baijunyao.com；
本地的话就是我们配置的虚拟主机自定义的本地域名；
DB_DATABASE 就是我们的数据库名比如说 test；
DB_USERNAME 数据库用户名比如说 root ；
DB_PASSWORD 数据库密码比如说 123456 ；

##### vendor 目录则不用我们手动创建；
我们在根目录下执行 composer install 命令；

###### 执行composer install遇到错误：Your requirements could not be resolved to an installable set of packages. 这是因为不匹配composer.json要求的版本。


```
D:\phpstudy_pro\WWW\laravel-blog-api>composer install
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Warning: The lock file is not up to date with the latest changes in composer.json. You may be getting outdated dependencies. Run update to update them.
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - Installation request for laravel/horizon v3.1.2 -> satisfiable by laravel/horizon[v3.1.2].
    - laravel/horizon v3.1.2 requires ext-pcntl * -> the requested PHP extension pcntl is missing from your system.

  To enable extensions, verify that they are enabled in your .ini files:
    - D:\phpstudy_pro\Extensions\php\php7.3.4nts\php.ini
  You can also run `php --ini` inside terminal to see which files are used by PHP in CLI mode.
```
提示我的PHP 7版本太高，不符合composer.json需要的版本，但是在PHP 7下应该也是可以运行的，composer可以设置忽略版本匹配，命令是：



```
composer install --ignore-platform-reqs
```


##### ###### 接着我们需要执行下 php artisan key:generate ;这个是生APP_KEY并自动写入到 .env 文件中的；它是 laravel 用来加密 cookie 等的；

![image](https://baijunyao.com/uploads/article/20180415/5ad36a5be0945.jpg)


```
php artisan migrate 生成数据表
```


```
php artisan db:seed  数据填充
```


### # 复制配置文件，并修改配置文件，修改数据名称和密码，启动redis，配置redis密码（文章点击量使用redis统计）
cp .env.example .env

### # 生成key
php artisan key:generate

#### # 生成jwt-key
php artisan jwt:secret

#### # 生成数据库表
php artisan migrate

#### # 填充数据
php artisan db:seed

#### # 或者合成一步 清空数据库重新生成表并生成数据
php artisan migrate --seed

#### # postman请求头设置herders (错误时才会返回json格式)
X-Requested-With => XMLHttpRequest

#### # 启动8080端口
php artisan serve --port=8080

#### # 查看接口版本号
http://localhost:8080/api/version



#### pcntl 的 php 扩展需要 平台的支持,对 windows 并不友好,我们可以尝试忽略在 commposer 安装时忽略平台使用的相关要求.

建议在 composer.json 文件中配置忽略配置.



```
"config": {
        "preferred-install": "dist",
        "sort-packages": true,
        "optimize-autoloader": true,
        "platform": {
            "ext-pcntl": "7.1",
            "ext-posix": "7.1"
        }
    }
```


数据填充
# 生成User模型的工厂
php artisan make:factory UserFactory --model=Models/User

# 生成User的数据填充
php artisan make:seeder UsersTableSeeder

# 数据填充
php artisan db:seed

# 填充指定模型
php artisan db:seed --class=UsersTableSeeder

# 重新生成数据库表并填充数据
php artisan migrate:refresh --seed

# 进入数据填充测试
php artisan tinker

# 生成20个用户模型
namespace App\Models;
factory(User::class, 20)->create();

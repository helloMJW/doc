<head>
     <title>EasySwoole 安装|swoole 安装|swoole 拓展</title>
     <meta name="keywords" content="EasySwoole 安装|swoole 安装|swoole 拓展"/>
     <meta name="description" content="EasySwoole 安装|swoole 安装|swoole 拓展"/>
     <script src="https://cdn.bootcss.com/layer/2.3/layer.js"></script>
     <link href="https://cdn.bootcss.com/layer/2.3/skin/layer.css" rel="stylesheet">
     <script>
      isNew = localStorage.getItem('isNew');
      if(isNew != 1){
         localStorage.setItem('isNew',1);
         window.onload = function () {
             layer.confirm('是否给EasySwoole点个赞',function (index) {
                 layer.msg('感谢您的支持');
                     setTimeout(function () {
                         window.open('https://github.com/easy-swoole/easyswoole');
                  },1500);
             });
         };  
      }
     </script>
</head>

---<head>---

# 框架安装

框架使用 `Composer` 作为依赖管理工具，在开始安装框架前，请确保已经按上一章节的要求配置好环境并安装好了`Composer` 工具，在安装过程中，会释放框架的文件到项目目录，请保证项目目录有可写入权限

- [GitHub](https://github.com/easy-swoole/easyswoole)  喜欢记得给我们点个***star***
- [GitHub for Doc](https://github.com/easy-swoole/doc)

> 关于 Composer 的安装可以参照 [Composer中国全量镜像](https://pkg.phpcomposer.com/#how-to-install-composer) 的安装教程,另外 Composer中国 已经很久没有更新了，请大家使用梯子或者是其他镜像。

## 切换阿里云镜像
````
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
````
## Composer 安装

按下面的步骤进行手动安装

```bash
composer require easyswoole/easyswoole=3.x
php vendor/bin/easyswoole install
```

> 如果安装遇到报错，请看下面的报错处理

## 报错处理

在一些环境中，特别是使用集成面板安装的 PHP 环境，会出现以下报错：

```bash
dir=$(d=${0%[/\\]*}; cd "$d" > /dev/null; cd '../easyswoole/easyswoole/bin' && pwd)

# See if we are running in Cygwin by checking for cygpath program
if command -v 'cygpath' >/dev/null 2>&1; then
    # Cygwin paths start with /cygdrive/ which will break windows PHP,
    # so we need to translate the dir path to windows format. However
    # we could be using cygwin PHP which does not require this, so we
    # test if the path to PHP starts with /cygdrive/ rather than /usr/bin
    if [[ $(which php) == /cygdrive/* ]]; then
        dir=$(cygpath -m "$dir");
    fi
fi

dir=$(echo $dir | sed 's/ /\ /g')
"${dir}/easyswoole" "$@"
```

引起该报错的大多数原因是因为当前PHP.ini禁用了`symlink`函数，或者无法在`vendor`目录下创建符号链接，可以执行以下命令行观察输出确认是否禁用了该函数，如果没有禁用该函数，则输出的列表中没有`symlink`字样

```bash
php -ini | grep disable_functions
```

> 如果禁用了该函数，可以直接修改PHP.ini或在集成面板中解除该函数的禁用，删除项目目录下的`vendor`目录，重新执行`composer install`拉取依赖包

如果是其他原因导致的该报错，可以在项目根目录下手工执行以下命令，将可执行文件链接出来 :

```bash
cd vendor/bin/ && rm -rf easyswoole && ln -s ../easyswoole/easyswoole/bin/easyswoole easyswoole && cd ../../
```

或者直接指向EasySwoole的管理脚本执行安装命令 : 

```bash
php vendor/easyswoole/easyswoole/bin/easyswoole install
```

## 手动安装

按下面的步骤进行手动安装

```bash
composer require easyswoole/easyswoole=3.x
php vendor/bin/easyswoole install
```

中途没有报错的话，执行：
```bash
# 启动框架
php easyswoole start
```
此时可以访问 `http://localhost:9501` 看到框架的欢迎页面，表示框架已经安装成功

> 如果第二步的 install 操作报错 请查看上方的报错处理

## Docker

[Easyswoole官方docker](docker.md)

> docker 知识请自行学习

## Hello World
在项目根目录下创建如下的目录结构，这个目录是编写业务逻辑的应用目录，编辑 `Index.php` 文件，添加基础控制器的代码

```
project              项目部署目录
----------------------------------
├─App        应用目录
│  └─HttpController      应用的控制器目录
│     └─Index.php    默认控制器文件
----------------------------------
```

```php
<?php
namespace App\HttpController;


use EasySwoole\Http\AbstractInterface\Controller;

class Index extends Controller
{

    function index()
    {
        // TODO: Implement index() method.
        $this->response()->write('hello world');
    }
}
```
然后编辑根目录下的 composer.json 文件，注册应用的命名空间

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "App/"
        }
    },
    "require": {
        "easyswoole/easyswoole": "3.x"
    }
}
```

> 实际上就是注册App的名称空间

最后执行 `composer dumpautoload` 命令更新命名空间，框架已经可以自动加载 **App** 目录下的文件了，此时框架已经安装完毕，可以开始编写业务逻辑

```bash
# 更新命名空间映射
composer dumpautoload
# 启动框架
php easyswoole start
```
启动框架后，访问 `http://localhost:9501`即可看到 Hello World 。

## 关于IDE助手

由于 Swoole 的函数并不是PHP标准函数，IDE无法进行自动补全，为了方便开发，可以执行以下命令引入IDE助手，在IDE下即可自动补全 Swoole 相关的函数

```bash
composer require easyswoole/swoole-ide-helper
```

## 目录结构

**EasySwoole** 的目录结构是非常灵活的，基本上可以任意定制，没有太多的约束，但是仍然建议遵循下面的目录结构，方便开发

```
project                   项目部署目录
├─App                     应用目录(可以有多个)
│  ├─HttpController       控制器目录
│  │  └─Index.php         默认控制器
│  └─Model                模型文件目录
├─Log                     日志文件目录
├─Temp                    临时文件目录
├─vendor                  第三方类库目录
├─composer.json           Composer架构
├─composer.lock           Composer锁定
├─EasySwooleEvent.php     框架全局事件
├─easyswoole              框架管理脚本
├─easyswoole.install      框架安装锁定文件
├─dev.php                 开发配置文件
├─produce.php             生产配置文件
```

> 如果项目还需要使用其他的静态资源文件，建议使用 **Nginx** / **Apache** 作为前端Web服务，将请求转发至 easySwoole 进行处理，并添加一个 `Public` 目录作为Web服务器的根目录

> 注意!请不要将框架主目录作为web服务器的根目录,否则dev.env,produce.env配置将会是可访问的,也可自行排除该文件(3.1.2已经改为dev.php,produce.php,但依旧建议设置到Public)

## 其他

- QQ交流群
    - VIP群 579434607 （本群需要付费599元）
    - EasySwoole官方一群 633921431(已满)
    - EasySwoole官方二群 709134628
    
- 商业支持：
    - QQ 291323003
    - EMAIL admin@fosuss.com
        
- 作者微信

     ![](../Resource/authWx.jpg)        
    
- [捐赠](../donate.md)
    您的捐赠是对Swoole项目开发组最大的鼓励和支持。我们会坚持开发维护下去。 您的捐赠将被用于:
        
  - 持续和深入地开发
  - 文档和社区的建设和维护



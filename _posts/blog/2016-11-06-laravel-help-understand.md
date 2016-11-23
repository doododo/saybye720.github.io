---
layout:   post
title:    laravel核心要点的个人理解帮助
description: 每次看手册都有不同的收获，这次整理下来以便以后的复习和深刻理解
category: blog
---

## 什么是服务容器

- 管理类的依赖和执行依赖注入

- 几乎所有的服务容器都是在服务提供者中完成

*容器不需要知道如何构建对象，它会使用PHP的反射自动解析出具体对象*

## 如何绑定容器

- 在服务提供者中，可以通过`$this->app`访问容器，使用`bind()`方法注册一个绑定

eg.

```
/**
 * @param  第一个参数是我们想要绑定的类名或者接口
 * @param  第二个参数是返回类实例的一个闭包
 */
$this->app->bind('rbac', function(){
  return new Rbac($app);
});
```

- 服务容器可以直接绑定接口实现接口

eg.

```
/**
 * 按照Log约定的接口实现SystemLog后，直接将其注册到服务容器
 */
$this->app->bind(
    \App\Contracts\Log::class,
    \App\Services\SystemLog::class
);
```

具体该实例的用法

eg.

```
use App\Contracts\Log;

class System {

    public $log;

    public function __construct(Log $log)
    {
        $this->log = $log;
    }
}
```

## 从容器中解析对象

eg.
```
$this->app->make(\Jenssegers\Agent\AgentServiceProvider::class);
or
app(\Jenssegers\Agent\AgentServiceProvider::class);
如果Facades里面定义可以直接
app('agent');
```

*关于服务提供者的理解*

## 什么是服务提供者

- 很多Laravel的核心类都是靠服务提供者启动，比如Event,Route,View等

- `config/app.php`文件里的providers数组是Laravel所有的服务提供者，只有当用到该服务提供者的时候，应用才会加载此服务

## 自定义服务提供者

eg.

```
namespace App\Providers;

use App\Libs\Rbac;
use Illuminate\Support\ServiceProvider;

class RbacServiceProvider extends ServiceProvider
{
    public function boot()
    {
        //
    }

    public function register()
    {
        // 绑定到容器
         $this->app->bind('rbac', function ($app) {
            return new Rbac($app);
        });
    }
}
```

## 如何延迟加载服务提供者

- 让服务提供者仅在服务容器中绑定，通过设置`$defer = true`让其服务提供者只有当被尝试解析的时候才会去加载

*关于Facade的理解*

- Facade就是为应用在服务容器绑定是提供一个静态接口，我的理解就是个代理

*构建Laravel第三方扩展石，最好不用Facade，因为其无法访问Facade测试的辅助函数*

## Facade实现原理

- 继承`Illuminate\Support\Facades\Facade`基类

- 实现getFacadeAccessor()方法，此方法规定从容器中解析什么，通俗作用就是返回服务容器绑定类的别名

- Facade基类通过`__callStatic()`从定义的Facade中调用解析的对象







# 初始化项目的步骤

## 1. 安装 operator-sdk 命令行
> brew install operator-sdk

## 2. 创建项目
> operator-sdk new sample-controller --repo github.com/liqiangblogdemos/sample-controller

sample-controller是项目的名称。
默认是开启了modules的， --repo 就是制定module的目录。

## 3. 添加CRD定义
> operator-sdk add api --api-version=admin.liqiang.io/v1 --kind=Admin

这个命令会在 pkg/apis 目录下看到生成的默认 CRD 的 Struct 代码了：

后面就需要在 admin_types.go 里面修改Admin 这个CR的 spec 和 status 

更新了之后都要执行下面的命令更新该资源类型的生成代码:
>operator-sdk generate k8s


## 4. 更新crd的yaml
>operator-sdk generate crds

这个命令会在 `deploy/crds/` 下面更新最新的yaml定义。

## 5. 添加一个新的controller
>operator-sdk add controller --api-version=admin.liqiang.io/v1 --kind=Admin

然后就会在 pkg/controller 目录下生成 Controller 的代码框架了，这个时候就需要自己填充一下业务逻辑了，
代码的逻辑写在 `func (r *ReconcileAdmin) Reconcile(request reconcile.Request) (reconcile.Result, error)` 中即可：

这里的Reconcile函数就是保持k8s的状态与设置一致：
1. 获取到最新的CR状态之后，然后就可以基于这些状态，通过代码调整k8s。

## 6. 注册CRD到k8s apiserver
>kubectl create -f deploy/crds/admin.liqiang.io_admins_crd.yaml

## 运行：
> export OPERATOR_NAME=memcached-operator
> export WATCH_NAMESPACE=default

# 总结
整个过程基本只有两个步骤需要手动：

1. 编写 CRD：只需要添加 Spec
2. 编写 Controller：只需要填充 Reconcile 函数

其他时间，都是在敲命令行，这样讲整个繁琐的过程简化为了一个结构体的定义以及一个函数的编写，带来了很大的生产力提高，值得推荐。



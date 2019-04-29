<img src="https://github.com/kubernetes/kubernetes/raw/master/logo/logo.png" width="100">

k8s 分享分析源码过程

分析的内容将涉及三个文件夹：

/root/go/src/k8s.io/kubernetes/cmd ---每个组件的主函数入口

/root/go/src/k8s.io/kubernetes/pkg ---每个组件的具体功能实现代码

/root/go/src/k8s.io/kubernetes/vendor ---依赖

/root/go/src/k8s.io/kubernetes/staging 	---已经分库的项目


scheduler主要涉及的文件：

/root/go/src/k8s.io/kubernetes/cmd/kube-scheduler/scheduler.go  scheduler主函数入口位置，命令行参数初始化以及校验。

/root/go/src/k8s.io/kubernetes/pkg/scheduler/scheduler.go 调度框架代码。

/root/go/src/k8s.io/kubernetes/pkg/scheduler/core/generic_scheduler.go 优选出适合在该node上创建pod的算法

其它目录：
api: swagger输出接口文档用，基本是json源码

build：构建脚本文件（包含镜像）

plugin：插件

test：测试相关的工具

third_party：第三方工具

docs：使用文档

Godeps：项目依赖的Go的第三方包，比如docker客户端sdk，rest等

hack：工具箱，各种编译，构建，校验的脚本都在这。



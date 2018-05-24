---
title: 在 Java 中创建第一个可靠 Azure 微服务 | Microsoft Docs
description: 介绍如何创建具有无状态服务和有状态服务的 Microsoft Azure Service Fabric 应用程序。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 48546e84b94ad0c11a159b2f88f7e21f7eb6ae0e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="get-started-with-reliable-services"></a>Reliable Services 入门
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-quick-start.md)
> * [Linux 上的 Java](service-fabric-reliable-services-quick-start-java.md)
>
>

本文介绍 Azure Service Fabric Reliable Services 的基础知识，并演示如何创建和部署以 Java 编写的简单 Reliable Service 应用程序。 此 Microsoft Virtual Academy 视频还演示了如何创建无状态可靠服务：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>安装和设置
在开始之前，请确保已在计算机上设置 Service Fabric 开发环境。
如果需要设置环境，请转到[在 Mac 上开始使用](service-fabric-get-started-mac.md)或[在 Linux 上开始使用](service-fabric-get-started-linux.md)。

## <a name="basic-concepts"></a>基本概念
若要开始使用 Reliable Services，只需了解几个基本概念：

* **服务类型**：这是服务实现。 它由你编写的可扩展 `StatelessService` 的类、其中使用的任何其他代码或依赖项以及名称和版本号定义。
* **命名服务实例**：若要运行服务，需要创建服务类型的命名实例，就像创建类类型的对象实例一样。 事实上，服务实例是编写的服务类的对象实例化。
* **服务宿主**：创建的命名服务实例需在宿主中运行。 服务宿主是可以运行服务实例的进程。
* **服务注册**：通过注册可将所有对象融合在一起。 只有将服务类型注册到服务宿主中的 Service Fabric 运行时后，Service Fabric 才能创建该类型的可运行实例。  

## <a name="create-a-stateless-service"></a>创建无状态服务
首先创建 Service Fabric 应用程序。 适用于 Linux 的 Service Fabric SDK 包括一个 Yeoman 生成器，它为包含无状态服务的 Service Fabric 应用程序提供基架。 首先，请运行以下 Yeoman 命令：

```bash
$ yo azuresfjava
```

按照说明创建**可靠无状态服务**。 本教程将应用程序命名为“HelloWorldApplication”，将服务命名为“HelloWorld”。 结果包含 `HelloWorldApplication` 和 `HelloWorld` 的目录。

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>服务注册
必须将服务类型注册到 Service Fabric 运行时。 服务类型在 `ServiceManifest.xml` 中以及实现 `StatelessService` 的服务类中定义。 服务注册在进程主入口点中执行。 在本示例中，进程主入口点为 `HelloWorldServiceHost.java`：

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>实现服务

打开 **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**。 此类定义服务类型，可以运行任何代码。 服务 API 为代码提供两个入口点：

* 名为 `runAsync()` 的开放式入口点方法，可在其中开始执行任何工作负荷，包括长时间运行的计算工作负荷。

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* 一个通信入口点，可在其中插入选择的通信堆栈。 这就是可以开始接收来自用户和其他服务请求的位置。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

在本教程中，我们将重点放在 `runAsync()` 入口点方法上。 这是可以立即开始运行代码的位置。

### <a name="runasync"></a>RunAsync
当服务实例已放置并且可以执行时，平台将调用此方法。 对于无状态服务，这就意味着打开服务实例。 需要关闭服务实例时，将提供取消标记进行协调。 在 Service Fabric 中，服务实例的此打开-关闭循环可能会在服务的整个生存期内出现多次。 发生这种情况的原因多种多样，包括：

* 系统可能会移动服务实例以实现资源平衡。
* 代码中发生错误。
* 应用程序或系统升级。
* 基础硬件遇到中断。

Service Fabric 将管理此业务流程，以便保持服务的高度可用和适当均衡。

`runAsync()` 不应阻止同步。 runAsync 的实现应返回一个 CompletableFuture，以允许运行时继续执行。 如果工作负荷需要实现一个应该在 CompletableFuture 内完成的、长期运行的任务。

#### <a name="cancellation"></a>取消
取消工作负荷是一项由所提供的取消标记协调的协同操作。 系统会等任务结束后（成功完成、取消或出现故障）再执行下一步操作。 当系统请求取消时，请务必接受取消标记，完成所有任务，并尽快退出 `runAsync()`。 以下示例演示如何处理取消事件：

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

在此无状态服务示例中，计数存储在本地变量中。 不过，由于这是无状态服务，因此，所存储的值仅在其所在服务实例的当前生命周期中存在。 当服务移动或重新启动时，值就会丢失。

## <a name="create-a-stateful-service"></a>创建有状态服务
Service Fabric 引入了一种新的有状态服务。 有状态服务能够可靠地在服务本身内部保持状态，并与使用它的代码共置。 Service Fabric 无需将状态保存到外部存储，便可实现状态的高可用性。

要将计数器值从无状态转换为即使在服务移动或重新启动时仍高度可用并持久存在，需要有状态服务。

在 HelloWorld 应用程序所在的同一个目录中，可通过运行 `yo azuresfjava:AddService` 命令添加新服务。 选择“可靠的有状态服务”作为框架，并将服务命名为“HelloWorldStateful”。 

应用程序现在应该有两个服务：无状态服务 HelloWorld 和有状态服务 HelloWorldStateful。

有状态服务具有与无状态服务相同的入口点。 主要区别是是否有能够可靠地存储状态的状态提供程序。 Service Fabric 附带名为 Reliable Collections 的状态提供程序实现，可让你通过 Reliable State Manager 创建复制的数据结构。 有状态可靠服务默认使用此状态提供程序。

打开 **HelloWorldStateful -> src** 中的 HelloWorldStateful.java，该文件包含以下 RunAsync 方法：

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` 在有状态服务和无状态服务中的运行方式类似。 只不过在有状态服务中，平台将先代表你执行额外的工作，然后再执行 `RunAsync()`。 这项工作可能包括确保可靠状态管理器和可靠集合随时可供使用。

### <a name="reliable-collections-and-the-reliable-state-manager"></a>可靠集合与可靠状态管理器
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) 是一种字典实现，可用于将状态可靠地存储在服务中。 利用 Service Fabric 和 Reliable Hashmaps，可以将数据直接存储在服务中而无需外部持久性存储。 Reliable Hashmaps 可让数据具备高可用性。 Service Fabric 通过创建和管理服务的多个*副本*来实现此目的。 它还提供一个抽象 API，消除了管理这些副本及其状态转换所存在的复杂性。

Reliable Collections 可以存储任何 Java 类型（包括自定义类型），但需要注意以下几点：

* Service Fabric 通过跨节点复制状态，使状态具备高可用性；而 Reliable Hashmap 会将数据存储到每个副本上的本地磁盘中。 这意味着 Reliable Hashmaps 中存储的所有内容都必须可序列化。 
* 在 Reliable Hashmaps 上提交事务时，将复制对象以实现高可用性。 存储在 Reliable Hashmaps 中的对象保留在服务的本地内存中。 这意味着你有对象的本地引用。
  
   切勿转变这些对象的本地实例而不在事务中的可靠集合上执行更新操作。 这是因为对对象的本地实例的更改将不会自动复制。 必须将对象重新插回字典中，或在字典上使用其中一个*更新*方法。

Reliable State Manager 自动管理 Reliable Hashmaps。 无论何时何地，都可以根据名称向可靠状态管理器请求服务中的某个可靠集合。 可靠状态管理器可确保能取回引用。 不建议将可靠集合实例的引用存储在类成员变量或属性中。 请特别小心，确保在服务生命周期中随时会引用设置为某个实例。 可靠状态管理器将处理此工作，并已针对重复访问进行优化。


### <a name="transactional-and-asynchronous-operations"></a>事务和异步操作
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Reliable Hashmaps 上的操作是异步的。 这是因为可靠集合的写入操作执行 I/O 操作，以将数据复制并保存到磁盘。

Reliable Hashmap 操作是事务性的，因此可以跨多个 Reliable Hashmaps 和操作保持状态的一致。 例如，在单个事务中，从一个 Reliable Dictionary 中获取工作项、对其执行操作并将结果保存在 Reliable Hashmap 中。 这被视为原子操作，它可以保证整个操作要么成功，要么回滚。 如果将项取消排队之后、保存结果之前发生错误，则会回滚整个事务，并且项将保留在队列中以供处理。


## <a name="run-the-application"></a>运行应用程序

Yeoman 基架包含用于生成应用程序的 gradle 脚本，以及用于部署和删除应用程序的 bash 脚本。 若要运行应用程序，请先使用 gradle 构建应用程序：

```bash
$ gradle
```

这会生成可以使用 Service Fabric CLI 部署的 Service Fabric 应用程序包。

### <a name="deploy-with-service-fabric-cli"></a>使用 Service Fabric CLI 部署

Install.sh 脚本包含用于部署应用程序包的 Service Fabric CLI 命令。 运行 install.sh 脚本以部署此应用程序。

```bash
$ ./install.sh
```

## <a name="next-steps"></a>后续步骤

* [Service Fabric CLI 入门](service-fabric-cli.md)

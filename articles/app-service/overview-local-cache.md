---
title: 本地缓存
description: 了解本地缓存在 Azure App Service 中的工作原理，以及如何启用、调整大小和查询应用本地缓存的状态。
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 2a1fc4de572fbb8634f8f58452ce5f9b632023a5
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628787"
---
# <a name="azure-app-service-local-cache-overview"></a>Azure 应用服务本地缓存概述

> [!NOTE]
> 函数应用或容器化应用服务应用中不支持本地缓存，如[Windows 容器](app-service-web-get-started-windows-container.md)中或[Linux 应用服务](containers/app-service-linux-intro.md)。


Azure App Service 内容存储在 Azure 存储中，并以持久方式呈现为内容共享。 此设计旨在兼容各种应用，具有以下特点：  

* 内容跨应用的多个虚拟机 (VM) 实例共享。
* 内容是持久性的，运行应用即可对其进行修改。
* 在同一共享内容文件夹下提供日志文件和诊断数据文件。
* 发布新内容会直接更新内容文件夹。 可以通过 SCM 网站和运行的应用即时查看相同的内容（通常情况下，在文件发生更改时，可以通过 ASP.NET 之类的特定技术重启应用，以获取最新内容）。

虽然许多应用使用所有此类功能或其中一项功能，但某些应用只需要高性能的只读内容存储，此类存储可用性高且支持这些应用的运行。 这些应用可以充分利用特定本地缓存的 VM 实例。

Azure 应用服务本地缓存功能允许通过 Web 角色来查看内容。 存储内容是在站点启动后异步创建的，而该内容是对存储内容进行写后即丢弃式缓存的结果。 当缓存就绪时，将对站点进行切换，使之在已缓存内容的基础上运行。 在本地缓存上运行的应用具有以下优势：

* 不受访问 Azure 存储上的内容时出现的延迟的影响。
* 不受计划内升级/计划外停机以及 Azure 存储出现的任何其他中断事件（发生在提供内容共享服务的服务器上）的影响。
* 因存储共享更改而需要重新启动应用的次数较少。

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>本地缓存如何改变应用服务的行为
* D:\home  指向本地缓存，它是应用启动时在 VM 实例上创建的。 D:\local  继续指向特定于临时 VM 的存储。
* 本地缓存包含共享内容存储的 /site 和 /siteextensions 文件夹的一次性副本，分别位于 D:\home\site 和 D:\home\siteextensions。     当应用程序启动时，文件将复制到本地缓存中。 默认情况下，每个应用的两个文件夹的大小限制为 1 GB，但可以增加到 2 GB。 请注意，随着缓存大小的增加，加载缓存需要更长的时间。 如果复制的文件超出了本地缓存的大小，应用服务会以无提示方式忽略本地缓存并从远程文件共享读取。
* 本地缓存是可以读写的。 不过，如果应用移动了虚拟机，或者系统重启了应用，则会放弃所做的任何修改。 如果应用在内容存储中存储了任务关键型数据，请不要使用本地缓存。
* D:\home\LogFiles 和 D:\home\Data 包含日志文件和应用数据。____ 两个子文件夹本地存储在 VM 实例上，并定期复制到共享内容存储。 应用可以通过将日志文件和数据写入到这些文件夹来保留它们。 但是，复制到共享内容存储是最大努力，因此由于 VM 实例的突然崩溃，日志文件和数据可能会丢失。
* [日志流式处理](troubleshoot-diagnostic-logs.md#stream-logs)受最大努力副本的影响。 可以在流式传输的日志中观察到最多一分钟的延迟。
* 在共享内容存储中，对于使用本地缓存的应用， _LogFiles_ 和 _Data_ 文件夹的文件夹结构会发生变化。 它们之中现在出现了子文件夹，其遵循的命名模式为“唯一标识符”+ 时间戳。 每个子文件夹对应于应用正在其中运行或已运行的一个虚拟机实例。
* D:\home 中的其他文件夹保留在本地缓存中，不会复制到共享内容存储。__
* 通过任何支持的方法进行的应用部署都将直接发布到持久共享内容存储。 若要刷新本地缓存中的 D:\home\site 和 D:\home\siteextensions 文件夹，需要重新启动应用。____ 若要确保无缝的生命周期，请参阅本文后面提供的信息。
* SCM 站点的默认内容视图仍是共享内容存储的视图。

## <a name="enable-local-cache-in-app-service"></a>在应用服务中启用本地缓存
组合使用保留的应用设置即可配置本地缓存。 可以通过以下方法配置这些应用设置：

* [Azure 门户](#Configure-Local-Cache-Portal)
* [Azure 资源管理器](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>通过 Azure 门户配置本地缓存
<a name="Configure-Local-Cache-Portal"></a>

使用以下应用设置即可对每个 Web 应用启用本地缓存：`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure 门户应用设置：本地缓存](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>使用 Azure 资源管理器配置本地缓存
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>更改本地缓存中的大小设置
本地缓存大小默认为 **1 GB**， 其中包括从内容存储复制过来的 /site 和 /siteextensions 文件夹，以及任何本地创建的日志和数据文件夹。 若要增加此限制，请使用应用设置 `WEBSITE_LOCAL_CACHE_SIZEINMB`。 最高可将此大小增加到每个应用 **2 GB** (2000 MB)。 请注意，当大小增加时，加载本地缓存需要更长的时间。

## <a name="best-practices-for-using-app-service-local-cache"></a>使用应用服务本地缓存的最佳实践
建议将本地缓存与[过渡环境](../app-service/deploy-staging-slots.md)功能结合在一起使用。

* 将值为 `Always` 的*粘性*应用设置 `WEBSITE_LOCAL_CACHE_OPTION` 添加到**生产**槽。 如果使用的是 `WEBSITE_LOCAL_CACHE_SIZEINMB`，也可将其作为粘性设置添加到“生产”槽。
* 创建**过渡**槽，并发布到过渡槽。 如果获得了生产槽的本地缓存优势，则要想通过无缝的“构建-部署-测试”生命周期进行过渡，通常不需要将过渡槽设置为使用本地缓存。
* 针对“过渡”槽来测试站点。  
* 准备就绪以后，在过渡槽和生产槽之间执行[交换操作](../app-service/deploy-staging-slots.md#Swap)。  
* 粘性设置包含名称，会粘到某个槽上。 因此，将“过渡”槽交换成“生产”槽以后，该槽会继承本地缓存应用设置。 新交换的“生产”槽会在几分钟后以本地缓存为基础运行，并会在交换后进行槽预热的过程中预热。 因此，在槽交换完成后，“生产”槽会在本地缓存的基础上运行。

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>如何确定本地缓存是否适用于应用？
如果应用需要高性能且可靠的内容存储，在运行时不使用内容存储来写入关键数据，并且总大小不到 2 GB，则可确定本地缓存适用于应用。 可通过站点扩展“Azure Web 应用磁盘使用情况”获取 /site 和 /siteextensions 文件夹的总大小。

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>如何确定站点是否已切换为使用本地缓存？
如果在过渡环境中使用本地缓存功能，则在本地缓存预热之前，交换操作不会完成。 若要了解站点是否正在本地缓存的基础上运行，可查看工作进程环境变量 `WEBSITE_LOCALCACHE_READY`。 根据[工作进程环境变量](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable)页上的说明，访问多个实例上的工作进程环境变量。  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>刚发布的新更改内容似乎并没有在应用上显示出来， 为什么？
如果应用使用本地缓存，则需重新启动站点才能获取最新更改。 不想将更改发布到生产站点？ 请参阅前述最佳实践部分的槽选项。

### <a name="where-are-my-logs"></a>日志在哪里？
使用本地缓存时，日志和数据文件夹看起来稍有不同。 但是，子文件夹的结构始终是相同的，区别在于子文件夹嵌套在格式为“唯一 VM 标识符”+ 时间戳的子文件夹下。

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>本地缓存已启用，但应用仍重新启动。 为什么会这样？ 本以为本地缓存可以缓解应用频繁重启的情况。
本地缓存确实可以防止与存储相关的应用重启情况。 但是，在对 VM 进行计划内基础结构升级时，应用仍可能重新启动。 启用本地缓存以后，总体说来应用重启的次数应该会少一些。

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>本地缓存是否会阻止某些目录被复制到更快的本地驱动器？
在复制存储内容过程中，将排除任何名为存储库的文件夹。 如果站点内容包含应用日常操作中可能不必要的源控件存储库，则此方法非常有用。 

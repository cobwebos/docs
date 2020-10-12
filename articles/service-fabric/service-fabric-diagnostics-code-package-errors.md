---
title: 使用 Service Fabric 诊断常见代码包错误
description: 了解如何使用 Azure Service Fabric 排查常见的代码包错误
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 983d45a7a240701fa6441d2d9edeeda16f1ed18b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256486"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>使用 Service Fabric 诊断常见代码包错误

本文介绍代码包意外终止的含义。 其中提供了常见错误代码的可能原因以及故障排除步骤。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>进程或容器何时意外终止？

当 Azure Service Fabric 收到启动代码包的请求时，它会根据应用和服务清单中设置的选项，开始在本地系统上准备环境。 这些准备工作可能包括保留网络终结点或资源、配置防火墙规则或设置资源监管约束。 

正确配置环境后，Service Fabric 将尝试打开代码包。 如果 OS 或容器运行时报告进程或容器已成功激活，则此步骤被视为成功。 如果激活失败，你将在 SFX 中看到如下所示的运行状况消息：

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

代码包成功激活后，Service Fabric 将开始监视该包的生存期。 此时，进程或容器可随时出于多种原因而终止。 例如，可能的原因包括无法初始化某个 DLL，或者 OS 耗尽了桌面堆空间。 如果代码包已终止，你将在 SFX 中看到以下运行状况消息：

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

此运行状况消息中的退出代码是进程或容器提供的有关其终止原因的唯一线索。 此代码可能由任何堆栈级别生成。 例如，此退出代码可能与 OS 错误或 .NET 问题相关，或者由程序代码引发。 可使用本文作为起点来诊断终止退出代码的原因并找到可能的解决方法。 但请注意，这些通用解决方法适用于常见场景，而不一定适用于你所看到的错误。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何判断 Service Fabric 是否终止了我的代码包？

Service Fabric 可能会出于各种原因而终止代码包。 例如，它可能决定将代码包放在另一个节点上，以实现负载均衡。 如果看到了下表中的任何退出代码，则可以确认 Service Fabric 已终止代码包。

>[!NOTE]
> 如果进程或容器终止并返回了退出代码，但该代码不是下表中所列的代码，则该进程或容器不是由 Service Fabric 终止的。

退出代码 | 说明
--------- | -----------
7147 | 表示 Service Fabric 已通过向进程或容器发送 Ctrl+C 信号正常将其关闭。
7148 | 表示 Service Fabric 终止了进程或容器。 有时，此错误代码表示在发送 Ctrl+C 信号后，进程或容器未及时做出响应，因此必须将其终止。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常见错误代码及其可能的修复方法

退出代码 | 十六进制值 | 简短说明 | 根本原因 | 可能的修复方法
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此错误有时表示计算机已耗尽桌面堆空间。 如果有极大量的进程属于节点上运行的应用程序，则这种可能性很大。 | 如果程序未在 Ctrl+C 信号响应方面经过设计，你可以在群集清单中启用 **EnableActivateNoWindow** 设置。 启用此设置后，代码包无需使用 GUI 窗口即可运行，并且不会收到 Ctrl+C 信号。 此操作还可减少每个进程占用的桌面堆空间量。 如果代码包需要接收 Ctrl+C 信号，你可以增加节点的桌面堆大小。
3762504530 | 0xe0434352 | 空值 | 此值表示托管代码（即 .NET）中发生的未经处理的异常的错误代码。 | 此退出代码表示应用程序引发了一个仍未处理的异常，从而终止了进程。 若要确定是哪种因素触发了此错误，首先请调试应用程序的日志和转储文件。

## <a name="next-steps"></a>后续步骤

* 详细了解如何 [诊断其他常见方案](service-fabric-diagnostics-common-scenarios.md)。
* 阅读 [Azure Monitor 概述](../azure-monitor/overview.md)，详细了解 Azure Monitor 日志及其提供的功能。
* 详细了解可帮助进行检测和诊断的 Azure Monitor 日志[警报](../azure-monitor/platform/alerts-overview.md)。
* 熟悉 Azure Monitor 日志中提供的[日志搜索和查询](../azure-monitor/log-query/log-query-overview.md)功能。

---
title: Azure Service Fabric 诊断代码包错误 |Microsoft Docs
description: 了解如何排查使用 Azure Service Fabric 的常见代码包错误
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276946"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>诊断与 Service Fabric 的常见代码包错误

本文说明了意外，终止的代码包的含义，并提供了深入的常见错误代码，以及故障排除步骤可能可能会消除此问题的可能原因。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>何时确实进程或容器终止了意外？

当 Service Fabric 收到请求以启动代码包时，它开始准备基于在应用程序和服务清单中设置的配置选项的本地系统上的环境。 保留网络终结点或资源、 配置防火墙规则，或设置资源调控限制，可能会包括这些准备。 一旦已正确配置环境，Service Fabric 会尝试打开的代码包。 此步骤将被视为成功，如果 OS 或容器运行时报告的进程或容器已成功激活。 如果激活未成功，应看到显示的 SFX 中的运行状况消息

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

一旦代码包具有已成功启动，Service Fabric 开始监视其生存期。 此时，进程或容器可以出于多种原因随时终止。 它可能无法初始化 DLL，操作系统可能已用完桌面堆空间，等等。如果代码包终止时，应看到显示的 SFX 中的运行状况消息

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

退出代码提供在此运行状况消息是关于它的终止原因进程/容器提供唯一的线索，它可能具有由堆栈的任何级别生成。 很难了解此退出代码是否与，例如，OS 错误，是.NET 问题，或由你的代码引发。 因此，本文可作为起始点诊断终止退出代码的源和可能的解决方案，请牢记这些是常见方案的常规解决方案，可能不适用于错误您会看到。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何知道是否 Service Fabric 终止我的代码包？

Service Fabric 可能负责终止的原因有多种将代码包。 例如，它可能决定在进行负载平衡的另一个节点上放置的代码包目的。 您可以知道是否代码包已终止由 Service Fabric 是否见到任何以下表中的退出代码：

>[!NOTE]
> 如果进程/容器终止而不是下表中的退出代码，Service Fabric 并不负责终止它。

退出代码 | 描述
--------- | -----------
7147 | 这些错误代码指示的 Service Fabric 正常关闭进程/容器通过将其发送 Ctrl + C 信号。
7148 | 这些错误代码指示 Service Fabric 终止进程/容器。 有时，此错误代码可以指示进程/容器未及时响应发送 Ctrl + C 信号，因此，必须要终止后。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常见的错误代码和其可能的修补程序

退出代码 | 十六进制值 | 简短说明 | 根本原因 | 潜在修复
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此错误可能可以表示计算机已用完桌面堆空间。 此原因是更有可能，如果有大量的属于在节点上运行的应用程序的进程。 | 如果您的程序没有响应 Ctrl + C 信号而生成的可以启用在群集清单中的"EnableActivateNoWindow"设置。 启用此设置将表示的代码包没有 GUI 窗口将运行并将不会收到 Ctrl + C 信号，但同时会降低桌面堆的每个进程占用的空间量。 如果代码包需要接收 Ctrl + C 信号，则可以增加节点的桌面堆的大小。
3762504530 | 0xe0434352 | 不适用 | 此值是从托管代码 (即，.NET) 未经处理的异常的错误代码。 | 如果你会看到此退出代码，则意味着你的应用程序引发的异常保持未处理且终止了进程。 应用程序的日志和转储调试时应确定错误的原因的第一步。

## <a name="next-steps"></a>后续步骤

* 详细了解[诊断其他常见方案](service-fabric-diagnostics-common-scenarios.md)
* 获取 Azure Monitor 日志和它通过读取提供的更详细的概述[什么是 Azure Monitor 日志？](../operations-management-suite/operations-management-suite-overview.md)
* 要详细了解 Azure Monitor 日志[警报](../log-analytics/log-analytics-alerts.md)以辅助检测和诊断。
* 掌握[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)作为 Azure Monitor 日志的一部分提供的功能

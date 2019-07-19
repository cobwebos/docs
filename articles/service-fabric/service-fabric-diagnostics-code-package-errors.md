---
title: 使用 Service Fabric 诊断常见代码包错误 |Microsoft Docs
description: 了解如何排查 Azure Service Fabric 的常见代码包错误
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
ms.openlocfilehash: 320a55e8b14648b1d7e256855582ab31846a63cf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249224"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>使用 Service Fabric 诊断常见代码包错误

本文介绍了代码包意外终止的含义。 它提供了常见错误代码的可能原因以及故障排除步骤。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>进程或容器何时意外终止？

当 Azure Service Fabric 收到启动代码包的请求时, 它会根据应用和服务清单中设置的选项, 开始在本地系统上准备环境。 这些准备工作可能包括保留网络终结点或资源、配置防火墙规则或设置资源监管约束。 

正确配置环境后, Service Fabric 会尝试打开代码包。 如果 OS 或容器运行时报告已成功激活进程或容器, 则此步骤将视为成功。 如果激活失败, 则应看到如下所示的运行状况消息:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

成功激活代码包后, Service Fabric 开始监视其生存期。 此时, 进程或容器可随时终止, 原因有多种。 例如, 它可能无法初始化 DLL, 或操作系统可能用尽了桌面堆空间。 如果你的代码包已终止, 你应在 SFX 中看到以下运行状况消息:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

此运行状况消息中的退出代码是进程或容器提供其终止原因的唯一提示。 它可以由堆栈的任何级别生成。 例如, 此退出代码可能与操作系统错误或 .NET 问题相关, 或者你的代码可能已引发。 使用本文作为诊断出口终止代码和可能的解决方案来源的起点。 但请记住, 这些是常见方案的一般解决方案, 可能不适用于你所看到的错误。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何判断 Service Fabric 是否终止了我的代码包？

Service Fabric 可能会出于各种原因而负责终止代码包。 例如, 它可能决定将代码包放在另一个节点上, 以实现负载平衡。 如果看到下表中的任何退出代码, 你可以验证 Service Fabric 是否终止了代码包。

>[!NOTE]
> 如果您的进程或容器使用退出代码终止, 而不是下表中的代码, 则 Service Fabric 不负责终止它。

退出代码 | 描述
--------- | -----------
7147 | 指示 Service Fabric 通过按 Ctrl + C 信号发送进程或容器来正常关闭。
7148 | 指示 Service Fabric 终止了进程或容器。 有时, 此错误代码表示进程或容器在发送 Ctrl + C 信号后没有及时响应, 必须终止。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常见错误代码及其潜在修补程序

退出代码 | 十六进制值 | 简短说明 | 根本原因 | 潜在修复
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此错误有时意味着计算机已用尽桌面堆空间。 如果你有多个属于节点上运行的应用程序的进程, 这可能会导致此问题。 | 如果程序未构建为响应 Ctrl + C 信号, 则可以启用群集清单中的**EnableActivateNoWindow**设置。 如果启用此设置, 则表示代码包将在没有 GUI 窗口的情况下运行, 并且不会收到 Ctrl + C 信号。 此操作还可减少每个进程使用的桌面堆空间量。 如果代码包需要接收 Ctrl + C 信号, 可以增加节点的桌面堆的大小。
3762504530 | 0xe0434352 | 不可用 | 此值表示托管代码中的未经处理的异常 (即 .NET) 的错误代码。 | 此退出代码指示应用程序引发了一个异常, 该异常将保持未处理的异常并终止进程。 作为确定触发此错误的方式的第一步, 调试应用程序的日志和转储文件。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[诊断其他常见方案](service-fabric-diagnostics-common-scenarios.md)。
* 阅读[Azure Monitor 概述](../operations-management-suite/operations-management-suite-overview.md), 更详细地了解 Azure Monitor 日志和这些日志提供的内容。
* 详细了解有关 Azure Monitor 日志的[警报](../log-analytics/log-analytics-alerts.md), 以便在检测和诊断方面获得帮助。
* 熟悉作为 Azure Monitor 日志一部分提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能。

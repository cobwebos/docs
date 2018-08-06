---
title: Azure Service Fabric 网格应用程序中的监控和诊断 | Microsoft Docs
description: 了解在 Azure Service Fabric 网格中如何监控和诊断应用程序。
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f857c23b5500bc7790a0ff7fcf81eaec51f37c9
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358244"
---
# <a name="monitoring-and-diagnostics"></a>监视和诊断
Azure Service Fabric 网格是一个完全托管的服务，由此开发者可部署微服务应用程序，而无需管理虚拟机、存储或网络。 Service Fabric 网格的监控和诊断分为三大类型诊断数据：

- 应用程序日志 - 这是基于应用程序检测方式的容器化应用程序的日志（如 docker 日志）
- 平台事件 - 网格平台中与容器操作相关的事件，当前包括容器激活、停用和终止。
- 容器指标 - 容器的资源利用率和性能指标（docker 统计数据）

本文介绍可用的最新预览版本的监视和诊断选项。

## <a name="application-logs"></a>应用程序日志

可基于每个容器，从已部署的容器查看 docker 日志。 在 Service Fabric 网格应用程序模型中，每个容器是应用程序中的代码包。 要查看代码包的相关日志，请使用以下命令：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> 可使用“az mesh service-replica”命令获取副本名称。 副本名称是从 0 开始递增的数字。*

以下是在投票应用程序的 VotingWeb.Code 容器中查看日志的示例：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>后续步骤
若要详细了解 Service Fabric 网格，请阅读概述：
- [Service Fabric 网格概述](service-fabric-mesh-overview.md)

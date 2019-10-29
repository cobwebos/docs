---
title: Azure 资源日志概述 |Microsoft Docs
description: 了解 Azure 资源日志支持的服务和事件架构。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989620"
---
# <a name="azure-resource-logs-overview"></a>Azure 资源日志概述
Azure 资源日志是由 Azure 资源发出的[平台日志](platform-logs-overview.md)，描述了其内部操作。 所有资源日志共享一个通用顶级架构，并为每个服务提供灵活性，以便为自己的事件发出唯一属性。

> [!NOTE]
> 资源日志以前称为诊断日志。

## <a name="collecting-resource-logs"></a>收集资源日志
资源日志由受支持的 Azure 资源自动生成，但不收集这些资源，除非使用[诊断设置](diagnostic-settings.md)对其进行配置。 为每个 Azure 资源创建诊断设置，以便将日志转发到以下目标：

| 目标 | 场景 |
|:---|:---|:---|
| [Log Analytics 工作区](resource-logs-collect-workspace.md) | 使用其他监视数据分析日志，并利用日志查询和日志警报等 Azure Monitor 功能。 |
| [Azure 存储](resource-logs-collect-storage.md) | 存档日志以进行审核或备份。 |
| [事件中心](resource-logs-stream-event-hubs.md) | 将日志流式传输到第三方日志记录和遥测系统。  |

## <a name="compute-resources"></a>计算资源
资源日志不同于 Azure 计算资源中的来宾 OS 级日志。 计算资源需要代理从其来宾操作系统收集日志和指标，包括诸如事件日志、syslog 和性能计数器这样的数据。 使用[诊断扩展](agents-overview.md#azure-diagnostic-extension)从 azure 虚拟机和[Log Analytics 代理](agents-overview.md#log-analytics-agent)路由日志数据，以便从 azure 中的虚拟机、其他云中以及本地到 Log Analytics 工作区收集日志和指标。 有关详细信息，请参阅[Azure Monitor 的监视数据源](data-sources.md)。

## <a name="resource-logs-schema"></a>资源日志架构
有关资源日志架构和类别的详细信息，请参阅[资源日志架构](diagnostic-logs-schema.md)。 

## <a name="next-steps"></a>后续步骤

* [了解更多有关](platform-logs-overview.md)可用于监视 Azure 的 azure 平台日志的信息。

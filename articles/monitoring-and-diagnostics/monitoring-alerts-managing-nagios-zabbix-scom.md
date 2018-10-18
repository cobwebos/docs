---
title: 管理来自其他监视服务的警报
description: 在 Azure Monitor 中管理 Nagios、Zabbix 和 SCOM 警报
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: d9d0cb326fb063e0a6bbfaab6a85961ab2b35416
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389358"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>管理来自其他监视服务的警报

现在可以在[统一警报体验](https://aka.ms/azure-alerts-overview)中查看来自 Nagios、Zabbix 和 System Center Operations Manager 的警报。 这些警报来自 Log Analytics 与 Nagios/Zabbix 服务器或 System Center Operations Manager 的集成。 

## <a name="prerequisites"></a>先决条件
Log Analytics 存储库中具有 Alert 类型的任何记录都将导入到统一警报体验中，因此必须执行收集这些记录所需的配置。
1. 对于 **Nagios** 和 **Zabbix** 警报，[配置这些服务器](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)以将警报发送到 Log Analytics。
1. 对于 **System Center Operations Manager** 警报，[将 Operations Manager 管理组连接到 Log Analytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)。 System Center Operations Manager 中创建的任何警报均导入 Log Analytics。

## <a name="view-your-alert-instances"></a>查看警报实例
将导入配置到 Log Analytics 中后，可以开始在[统一警报体验](https://aka.ms/azure-alerts-overview)中从这些监视服务查看警报实例。 这些警报实例出现在统一警报体验中后，你就可以[管理警报实例](https://aka.ms/managing-alert-instances)、[管理基于这些警报创建的智能组](https://aka.ms/managing-smart-groups)以及[更改警报和智能组的状态](https://aka.ms/managing-alert-smart-group-states)。

> [!NOTE]
>  统一警报体验中的 Nagios 警报不具有状态 - 例如，警报的[监视条件](https://aka.ms/azure-alerts-overview)不会从“已触发”变为“已解决”。 而是，“已触发”和“已解决”都显示为单独的警报实例。 

---
title: 在 Azure Monitor 中管理来自 System Center Operations Manager、Zabbix 和 Nagios 的警报
description: 在 Azure Monitor 中管理来自 System Center Operations Manager、Zabbix 和 Nagios 的警报
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105883"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>在 Azure Monitor 中管理来自 System Center Operations Manager、Zabbix 和 Nagios 的警报

现在，可以在 [Azure Monitor](./alerts-overview.md) 中查看来自 Nagios、Zabbix 和 System Center Operations Manager 的警报。 这些警报来自 Log Analytics 与 Nagios/Zabbix 服务器或 System Center Operations Manager 的集成。 

## <a name="prerequisites"></a>必备条件
Log Analytics 存储库中类型为 Alert 的任何记录都将导入到 Azure Monitor 中，因此，你必须执行收集这些记录所需的配置。
1. 对于 **Nagios** 和 **Zabbix** 警报，[配置这些服务器](../learn/quick-collect-linux-computer.md)以[将警报发送](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json)到 Log Analytics。
1. 对于 **System Center Operations Manager** 警报，[将 Operations Manager 管理组连接到 Log Analytics 工作区](./om-agents.md)。 此后，部署来自 Azure 解决方案市场的[警报管理](./alert-management-solution.md)解决方案。 部署完成后，在 System Center Operations Manager 中创建的任何警报都将导入到 Log Analytics 中。

## <a name="view-your-alert-instances"></a>查看警报实例
在配置了到 Log Analytics 中的导入后，可以开始在 [Azure Monitor](./alerts-overview.md) 中查看来自这些监视服务的警报实例。 这些警报实例出现在 Azure Monitor 中后，你可以[管理警报实例](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json)、[管理基于这些警报创建的智能组](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json)以及[更改警报和智能组的状态](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json)。

> [!NOTE]
>  1. 此解决方案仅允许在 Azure Monitor 中查看 System Center Operations Manager/Zabbix/Nagios 触发的警报实例。 警报规则配置只能在相应的监视工具中查看/编辑。 
>  1. 所有已触发的警报实例都将在 Azure Monitor 和 Azure Log Analytics 中提供。 目前，无法在两者之间进行选择，也无法仅引入已触发的特定警报。
>  1. 来自 System Center Operations Manager、Zabbix 和 Nagios 的所有警报的信号类型都是“未知”，因为基础遥测类型不可用。
>  1. Nagios 警报不具有状态 - 例如，警报的[监视条件](./alerts-overview.md)将不会从“已触发”变为“已解决”。 而是，“已触发”和“已解决”都显示为单独的警报实例。
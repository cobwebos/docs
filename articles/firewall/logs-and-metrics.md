---
title: Azure 防火墙日志和指标概述
description: 本文概述了 Azure 防火墙诊断日志和指标。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/22/2019
ms.author: victorh
ms.openlocfilehash: fea00358fc21cf6f57673e14ebd0feafe532b620
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876560"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure 防火墙日志和指标

可以使用防火墙日志来监视 Azure 防火墙。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。

可通过门户访问其中部分日志。 可将日志发送到 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)、存储和事件中心，并使用 Azure Monitor 日志或其他工具（例如 Excel 和 Power BI）对其进行分析。

指标是轻型的, 可以支持近实时方案, 使其对警报和快速的问题检测非常有用。 

## <a name="diagnostic-logs"></a>诊断日志

 以下诊断日志适用于 Azure 防火墙：

* **应用程序规则日志**

   应用程序规则日志将保存到存储帐户, 并传输到事件中心, 并/或仅在为每个 Azure 防火墙启用了此日志的情况下将其发送到 Azure Monitor 日志。 每当建立与某个配置的应用程序规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **网络规则日志**

   网络规则日志将保存到存储帐户, 并传输到事件中心, 并/或仅在为每个 Azure 防火墙启用了日志后, 才能将其发送到 Azure Monitor 日志。 每当建立与某个配置的网络规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

可通过三种方式存储日志：

* **存储帐户**：如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。
* **事件中心**：若要集成其他安全信息和事件管理 (SEIM) 工具，获取资源警报，则事件中心是很好的选择。
* **Azure Monitor 日志**：Azure Monitor 日志最适合用于应用程序常规实时监视或查看趋势。

## <a name="activity-logs"></a>活动日志

   默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。

   可以使用[azure 活动日志](../azure-resource-manager/resource-group-audit.md)(以前称为操作日志和审核日志) 查看提交到 Azure 订阅的所有操作。

## <a name="metrics"></a>指标

Azure Monitor 中的度量值是在特定时间描述系统某些方面的数值。 度量值每分钟收集一次, 可用于报警, 因为它们可以频繁采样。 使用相对简单的逻辑可以快速触发警报。

以下指标适用于 Azure 防火墙:

- **应用程序规则命中计数**-应用程序规则命中次数。

    单位: 计数

- **网络规则命中计数**-网络规则的命中次数。

    单位: 计数

- **处理的数据**-遍历防火墙的数据量。

    单位: 字节

- **防火墙运行状况状态**-指示防火墙的运行状况。

    单位: 百分比

   此指标有两个维度:
  - **状态**：可能值为 "*正常*"、"已*降级*" 和 "不*正常*"。
  - **原因**:指示防火墙的相应状态的原因。 例如, 如果防火墙状态为降级或不正常, 则它可以指示*SNAT 端口*。





- **SNAT 端口使用率**-防火墙使用的 snat 端口数的百分比。

    单位: 百分比

   将更多公共 IP 地址添加到防火墙时, 可以使用更多 SNAT 端口, 从而减少 SNAT 端口利用率。 此外, 当防火墙出于不同的原因 (例如, CPU 或吞吐量) 而扩展时, 附加的 SNAT 端口也会变得可用。 因此, 在没有添加任何公共 IP 地址的情况下, 在不添加任何公共 IP 地址的情况下, 不需要添加任何公共 IP 地址, 而只是因为服务已缩小。可以直接控制可用于增加防火墙上可用端口的公共 IP 地址数。 但不能直接控制防火墙缩放。 目前, 仅为前五个公共 IP 地址添加了 SNAT 端口。   


## <a name="next-steps"></a>后续步骤

- 若要了解如何监视 Azure 防火墙日志和指标，请参阅[教程：监视 Azure 防火墙日志](tutorial-diagnostics.md)。

- 若要详细了解 Azure Monitor 中的指标, 请参阅[Azure Monitor 中的度量值](../azure-monitor/platform/data-platform-metrics.md)。
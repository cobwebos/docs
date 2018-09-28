---
title: Azure 防火墙日志概述
description: 本文是 Azure 防火墙诊断日志的概述。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6d8d0b2dc0f6baf48d2aacb9c7203937aef08d15
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957791"
---
# <a name="azure-firewall-logs"></a>Azure 防火墙日志

可以使用防火墙日志来监视 Azure 防火墙。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。

可通过门户访问其中部分日志。 可将日志发送到 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、存储和事件中心，并使用 Log Analytics 或其他工具（例如 Excel 和 Power BI）对其进行分析。

## <a name="diagnostic-logs"></a>诊断日志

 以下诊断日志适用于 Azure 防火墙：

* **应用程序规则日志**

   仅当为每个 Azure 防火墙启用了应用程序规则日志时，才会将此日志保存到存储帐户、流式传输到事件中心，和/或发送到 Log Analytics。 每当建立与某个配置的应用程序规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

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

   仅当为每个 Azure 防火墙启用了网络规则日志时，才会将此日志保存到存储帐户、流式传输到事件中心，和/或发送到 Log Analytics。 每当建立与某个配置的网络规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

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

* 存储帐户：如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。
* 事件中心：若要集成其他安全信息和事件管理 (SEIM) 工具，获取资源警报，则事件中心是很好的选择。
* Log analytics：Log analytics 最适合用于应用程序常规实时监视或查看趋势。

## <a name="activity-logs"></a>活动日志

   默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。

   可以使用 [Azure 活动日志](../azure-resource-manager/resource-group-audit.md)（以前称为运行日志和审核日志）查看提交到 Azure 订阅的所有操作。


## <a name="next-steps"></a>后续步骤

若要了解如何监视 Azure 防火墙日志和指标，请参阅[教程：监视 Azure 防火墙日志](tutorial-diagnostics.md)。
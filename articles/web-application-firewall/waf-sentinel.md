---
title: 将 Azure Sentinel 用于 Azure Web 应用程序防火墙
description: '本文介绍如何将 Azure Sentinel 与 Azure Web 应用程序防火墙一起使用 (WAF) '
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 525ec334e73ca010d319b40ab864d08dae32f493
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997227"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>将 Azure Sentinel 用于 Azure Web 应用程序防火墙

Azure Web 应用程序防火墙 (WAF) 与 Azure Sentinel 结合，可以为 WAF 资源提供安全信息事件管理。 Azure Sentinel 使用 Log Analytics 提供安全分析，使你可以轻松地分解和查看 WAF 数据。 使用 Sentinel，你可以访问预建的工作簿并对其进行修改，以最大程度地满足组织的需求。 工作簿可显示 Azure 内容传送网络上的 WAF 分析 (CDN) 、Azure 前门上的 WAF，以及跨多个订阅和工作区在应用程序网关上 WAF。

## <a name="waf-log-analytics-categories"></a>WAF log analytics 类别

WAF log analytics 分为以下几类：  

- 执行的所有 WAF 操作 
- 最常见的40请求 URI 地址 
- Top 50 事件触发器，  
- 随时间推移的消息 
- 完整的消息详细信息 
- 通过消息攻击事件  
- 随时间推移的攻击事件 
- 跟踪 ID 筛选器 
- 跟踪 ID 消息 
- 前10个攻击 IP 地址 
- IP 地址的攻击消息 

## <a name="waf-workbook-examples"></a>WAF 工作簿示例

以下 WAF 工作簿示例显示示例数据：

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF 操作筛选器":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="WAF 操作筛选器":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="WAF 操作筛选器":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="WAF 操作筛选器":::

## <a name="launch-a-waf-workbook"></a>启动 WAF 工作簿

WAF 工作簿适用于所有 Azure 前门、应用程序网关和 CDN Waf。 在连接这些资源中的数据之前，必须在资源上启用 log analytics。 

若要为每个资源启用 log analytics，请前往单个 Azure 前门、应用程序网关或 CDN 资源：

1. 选择“诊断设置”。
2. 选择“+ 添加诊断设置”。  
3. 在 "诊断设置" 页中：
   1. 键入名称。 
   1. 选择“发送到 Log Analytics”****。 
   1. 选择 "日志目标" 工作区。 
   1. 选择要分析的日志类型：
      1. 应用程序网关： "ApplicationGatewayAccessLog" 和 "ApplicationGatewayFirewallLog"
      1. Azure 前门： "FrontDoorAccessLog" 和 "FrontDoorFirewallLog"
      1. CDN： "AzureCdnAccessLog"
   1. 选择“保存”。

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="WAF 操作筛选器" 页面。

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="WAF 操作筛选器" 选项卡。选择一个推荐的工作簿。 此工作簿将使用以前启用的所有日志分析数据。 现在，你的 WAF 资源应该已经有一个工作 WAF 工作簿。

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF 操作筛选器":::


## <a name="next-steps"></a>后续步骤

- [了解有关 Azure Sentinel 的详细信息](../sentinel/overview.md)
- [了解 Azure Monitor 工作簿的详细信息](../azure-monitor/platform/workbooks-overview.md)

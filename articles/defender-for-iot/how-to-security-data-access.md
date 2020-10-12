---
title: 访问安全 & 建议数据
description: 了解如何在使用适用于 IoT 的 Defender 时访问安全警报和建议数据。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: e56cf54e1bf1483309cb7aac8519bb281ca2bc06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934161"
---
# <a name="access-your-security-data"></a>访问安全数据

如果你选择将其保存) 在 Log Analytics 工作区中，则用于 IoT 的 Defender 存储安全警报、建议和原始安全数据 (。

## <a name="log-analytics"></a>Log Analytics

配置使用的 Log Analytics 工作区：

1. 打开 IoT 中心
1. 单击 "**安全性**" 部分下的 "**设置**" 边栏选项卡。
1. 单击 " **数据收集**"，然后更改 Log Analytics 工作区配置。

若要在配置后访问 Log Analytics 工作区中的警报和建议：

1. 选择用于 IoT 的警报或建议。
1. 单击 " **进一步调查**"，然后单击 **查看具有此警报的设备单击此处并查看 DeviceId 列**。

有关从 Log Analytics 查询数据的详细信息，请参阅 [Log Analytics 中的查询入门](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全警报

安全警报存储在为 _AzureSecurityOfThings_ IoT 解决方案配置的 Log Analytics 工作区中的 SecurityAlert 表中。

我们提供了许多有用的查询来帮助你开始浏览安全警报。

### <a name="sample-records"></a>示例记录

选择几个随机记录

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 说明                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18：10：29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 强力攻击成功           | 已成功对设备执行暴力攻击        |    {"完整源地址"： "[ \" 10.165.12.18： \" ]"，"用户名"： "[ \" \" ]"，"DeviceId"： "IoT-设备-Linux"}                                                                       |
| 2018-11-19T12：40：31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 设备上的本地登录成功      | 检测到成功的本地登录到设备     | {"远程地址"： "？"、"远程端口"： ""、"本地端口"： ""、"登录 Shell"： "/bin/su"、"登录过程 Id"： "28207"、"用户名"： "攻击者"、"DeviceId"： "IoT-设备-Linux"} |
| 2018-11-19T12：40：31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 设备上的本地登录尝试失败  | 检测到设备的本地登录尝试失败 |    {"远程地址"： "？"、"远程端口"： ""、"本地端口"： ""、"登录 Shell"： "/bin/su"、"登录过程 Id"： "22644"、"用户名"： "攻击者"、"DeviceId"： "IoT-设备-Linux"} |

### <a name="device-summary"></a>设备摘要

获取在过去一周内检测到的不同安全警报的数量，按 IoT 中心、设备、警报严重性和警报类型分组。

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 强力攻击成功           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 中型        | 设备上的本地登录尝试失败  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 设备上的本地登录成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 中型        | 加密硬币挖掘器                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中心摘要

选择在上周、IoT 中心、警报严重性、警报类型中包含警报的不同设备的数量

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | 高          | 强力攻击成功           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | 中型        | 设备上的本地登录尝试失败  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | 高          | 设备上的本地登录成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | 中型        | 加密硬币挖掘器                     | 1          |

## <a name="security-recommendations"></a>安全建议

安全建议存储在为 _AzureSecurityOfThings_ IoT 解决方案配置的 Log Analytics 工作区中的 SecurityRecommendation 表中。

我们提供了许多有用的查询来帮助你开始探索安全建议。

### <a name="sample-records"></a>示例记录

选择几个随机记录

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 说明 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10：21：06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 中型 | 可用 | 找到输入链中的可许可防火墙规则 | 已发现防火墙中包含广泛 IP 地址或端口的许可模式的规则 | {"Rules"： "[{ \" SourceAddress \" ： \" \" ， \" SourcePort \" ： \" \" ， \" DestinationAddress \" ： \" \" ， \" DestinationPort \" ： \" 1337 \" }]"} |
| 2019-03-22T10：50：27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 中型 | 可用 | 找到输入链中的可许可防火墙规则 | 已发现防火墙中包含广泛 IP 地址或端口的许可模式的规则 | {"Rules"： "[{ \" SourceAddress \" ： \" \" ， \" SourcePort \" ： \" \" ， \" DestinationAddress \" ： \" \" ， \" DestinationPort \" ： \" 1337 \" }]"} |

### <a name="device-summary"></a>设备摘要

获取不同的活动安全建议的数量，按 IoT 中心、设备、建议严重性和类型分组。

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 中型        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group><iot_hub> | <device_name> | 中型        | 4   |

## <a name="next-steps"></a>后续步骤

- 阅读用于 IoT 的 Defender [概述](overview.md)
- 了解 IoT[体系结构](architecture.md)的 Defender
- 了解并浏览 [用于 IoT 警报的 Defender](concept-security-alerts.md)
- 了解并浏览 [用于 IoT 的 Defender 建议](concept-recommendations.md)

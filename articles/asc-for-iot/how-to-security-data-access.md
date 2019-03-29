---
title: 访问数据的 IoT 预览版使用 ASC |Microsoft Docs
description: 了解有关如何对 IoT 使用 ASC 时访问你的安全警报和建议数据。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: d81a8973772879f4f4b143701a1f4be3ecad95d9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576633"
---
# <a name="access-your-security-data"></a>访问安全数据 

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版本没有附带服务级别协议提供，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

适用于 IoT 的 ASC 将存储的安全警报、 建议和原始安全数据 （如果您选择将其保存） 在 Log Analytics 工作区中。

## <a name="log-analytics"></a>Log Analytics

若要配置使用 Log Analytics 工作区：

1. 打开 IoT 中心
1. 单击**安全**
2. 单击**设置**，并将更改 Log Analytics 工作区配置。

若要完成配置后访问你的 Log Analytics 工作区：

1. 选择警报或建议在 ASC 中对 IoT 上。 
2. 单击**进一步调查**，然后单击**若要查看哪些设备具有此警报，请单击此处，查看 DeviceId 列**。

从 Log Analytics 查询数据的详细信息，请参阅[开始使用 Log Analytics 中查询](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全警报

安全警报存储在_AzureSecurityOfThings.SecurityAlert_配置为 ASC 为 IoT 解决方案的 Log Analytics 工作区中的表。

我们已提供多个有用的查询，以帮助您开始探索安全警报。

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 描述                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力破解攻击           | 在设备上的暴力破解攻击已成功        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在设备上的本地登录名      | 检测到成功的本地登录到设备     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 在设备上的本地登录尝试失败  | 检测到对设备的本地登录失败的尝试 |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary"></a>设备摘要

选择不同的安全警报由 IoT 中心、 设备、 警报严重性、 警报类型检测到过去一周数。

```
// Select number of distinct security alerts detected last week by 
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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力破解攻击           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型        | 在设备上的本地登录尝试失败  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在设备上的本地登录名      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型        | 加密硬币 Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中心摘要

选择多个非重复过去一周，通过 IoT 中心、 警报严重性、 警报类型都有过警报的设备

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功的暴力破解攻击           | 第          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中型        | 在设备上的本地登录尝试失败  | 第          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功在设备上的本地登录名      | 第          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中型        | 加密硬币 Miner                     | 第          |

## <a name="security-recommendations"></a>安全建议

中存储的安全建议_AzureSecurityOfThings.SecurityRecommendation_配置为 ASC 为 IoT 解决方案的 Log Analytics 工作区中的表。

我们提供了多个有用的查询，以帮助您开始探索安全建议。

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
    
| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 描述 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型 | 活动 | 找到输入链中的宽松的防火墙规则 | 在防火墙中找到了包含适用于各种 IP 地址或端口的宽容模式的规则 | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型 | 活动 | 找到输入链中的宽松的防火墙规则 | 在防火墙中找到了包含适用于各种 IP 地址或端口的宽容模式的规则 | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>设备摘要

选择通过 IoT 中心、 设备、 建议严重性和类型的不同活动的安全建议。

```
// Select number of distinct active security recommendations by 
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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型        | 第 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 第  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型        | 4   |


## <a name="next-steps"></a>后续步骤

- 适用于 IoT 读取 ASC[概述](overview.md)
- 了解适用于 IoT ASC[体系结构](architecture.md)
- 了解和探索[ASC 为 IoT 警报](concept-security-alerts.md)
- 了解和探索[ASC 为 IoT 建议](concept-recommendations.md)
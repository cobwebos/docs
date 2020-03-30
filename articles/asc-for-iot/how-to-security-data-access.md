---
title: 使用 Azure IoT 安全中心访问数据*微软文档
description: 了解如何在使用 Azure IoT 安全中心时访问安全警报和建议数据。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597177"
---
# <a name="access-your-security-data"></a>访问您的安全数据 

IoT 的 Azure 安全中心在日志分析工作区中存储安全警报、建议和原始安全数据（如果选择保存）。

## <a name="log-analytics"></a>Log Analytics

要配置使用日志分析工作区：

1. 打开 IoT 中心
1. 单击 **"安全**"部分下的 **"概述**"边栏
2. 单击 **"设置"** 并更改日志分析工作区配置。

要在配置后访问日志分析工作区中的警报和建议，请执行以下操作：

1. 在 IoT 的 Azure 安全中心中选择警报或建议。 
2. 单击**进一步调查**，然后单击"**查看哪些设备具有此警报"，请单击此处并查看 DeviceId 列**。

有关从日志分析查询数据的详细信息，请参阅[在日志分析中开始查询](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全警报

安全警报存储在为 IoT 解决方案的 Azure 安全中心配置的日志分析工作区中的_Azure 安全安全警报_表中。

我们提供了许多有用的查询，以帮助您开始探索安全警报。

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
| 2018-11-18T18：10：29.000 | /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 野蛮武力攻击成功           | 对设备的暴力攻击成功        |    •\""完整源地址"："10.165.12.18："\"\"用户名"："\"，"\"\"设备 Id"："IoT-Device-Linux" |                                                                       |
| 2018-11-19T12：40：31.000 | /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 设备上成功本地登录      | 检测到设备成功本地登录     | • "远程地址"："？"，"远程端口"：""、"本地端口"：""、"登录外壳"："/bin/su"，"登录进程 ID"："28207"，"用户名"："攻击者"、"设备 Id"："IoT-Device-Linux" | |
| 2018-11-19T12：40：31.000 | /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 设备上的本地登录尝试失败  | 检测到设备的本地登录尝试失败 |  • "远程地址"："？"，"远程端口"：""、"本地端口"：""、"登录外壳"："/bin/su"，"登录进程 ID"："22644"、"用户名"："攻击者"、"设备 Id"："IoT-Device-Linux" | |

### <a name="device-summary"></a>设备摘要

获取上周检测到的不同安全警报数，按 IoT 中心、设备、警报严重性、警报类型分组。

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
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 野蛮武力攻击成功           | 9   |   
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 中型        | 设备上的本地登录尝试失败  | 242 |    
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 设备上成功本地登录      | 31  |
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 中型        | 加密硬币矿工                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中心摘要

通过 IoT 中心、警报严重性、警报类型选择上周警报的多个不同设备

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
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | 高          | 野蛮武力攻击成功           | 1          |    
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | 中型        | 设备上的本地登录尝试失败  | 1          | 
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | 高          | 设备上成功本地登录      | 1          |
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | 中型        | 加密硬币矿工                     | 1          |

## <a name="security-recommendations"></a>安全建议

安全建议存储在为 IoT 解决方案的 Azure 安全中心配置的日志分析工作区中的_Azure 安全信息.安全建议_表中。

我们提供了许多有用的查询，以帮助您开始探索安全建议。

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
    
| TimeGenerated | IoTHubId | DeviceId | 建议严重性 | 建议国 | 推荐显示名称 | 描述 | 建议附加数据 |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10：21：06.060 | /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 中型 | 活动 | 在输入链中找到允许的防火墙规则 | 在防火墙中已发现一条规则，其中包含各种 IP 地址或端口的允许模式 | {"规则\""："]源地址\"：\"\"\"、\"源\"\"端口\"：\"\"\"、\"目的地\"地址\"： 、\"目的地端口 ： 1337 []] |
| 2019-03-22T10：50：27.237 | /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 中型 | 活动 | 在输入链中找到允许的防火墙规则 | 在防火墙中已发现一条规则，其中包含各种 IP 地址或端口的允许模式 | {"规则\""："]源地址\"：\"\"\"、\"源\"\"端口\"：\"\"\"、\"目的地\"地址\"： 、\"目的地端口 ： 1337 []] |

### <a name="device-summary"></a>设备摘要

获取按 IoT 中心、设备、建议严重性和类型分组的不同活动安全建议的数量。

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

| IoTHubId                                                                                                       | DeviceId      | 建议严重性 | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 2   |    
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 中型        | 1 |  
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /订阅/<subscription_id>/资源组/<resource_group>/提供商/微软.设备/IotHubs/<iot_hub> | <device_name> | 中型        | 4   |


## <a name="next-steps"></a>后续步骤

- 阅读 Azure 安全中心，了解 IoT[概述](overview.md)
- 了解适用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 了解并探索[适用于 IoT 警报的 Azure 安全中心](concept-security-alerts.md)
- 了解并探索[Azure 安全中心，了解 IoT 建议](concept-recommendations.md)

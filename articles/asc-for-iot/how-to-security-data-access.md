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
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541987"
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

1. 在 ASC 中选择一个警报，对 IoT。 
2. 单击**进一步调查**，然后单击**若要查看哪些设备具有此警报，请单击此处，查看 DeviceId 列**。

从 Log Analytics 查询数据的详细信息，请参阅[开始使用 Log Analytics 中查询](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全警报

安全警报存储在**ASCforIoT.SecurityAlert**已配置的 Log Analytics 工作区中的表。

使用以下基本 kql 查询来开始探索安全警报。

### <a name="sample-records-query"></a>示例记录查询

随机选择几个记录： 

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

#### <a name="sample-query-results"></a>示例查询结果 

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | 显示名称                           | 描述                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力破解攻击           | 在设备上的暴力破解攻击已成功        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在设备上的本地登录名      | 检测到成功的本地登录到设备     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 在设备上的本地登录尝试失败  | 检测到对设备的本地登录失败的尝试 |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary-query"></a>设备摘要查询

若要选择多个不同的安全警报此 kql 查询检测到的 IoT 中心、 设备、 警报严重性、 警报类型的最后一周的使用。

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

#### <a name="device-summary-query-results"></a>设备摘要查询结果

| IoTHubId | DeviceId| AlertSeverity| 显示名称 | 计数 |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力破解攻击           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型        | 在设备上的本地登录尝试失败  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在设备上的本地登录名      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中型        | 加密硬币 Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>IoT 中心摘要

使用此 kql 查询来选择多个不同的设备的警报在最后一周内，通过 IoT 中心、 警报严重性、 警报类型：

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

#### <a name="iot-hub-summary-query-results"></a>IoT 中心汇总查询结果

| IoTHubId                                                                                                       | AlertSeverity | 显示名称                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功的暴力破解攻击           | 第          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中型        | 在设备上的本地登录尝试失败  | 第          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功在设备上的本地登录名      | 第          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中型        | 加密硬币 Miner                     | 第          |



## <a name="next-steps"></a>后续步骤

- 适用于 IoT 读取 ASC[概述](overview.md)
- 了解适用于 IoT ASC[体系结构](architecture.md)
- 了解和探索[ASC 为 IoT 警报](concept-security-alerts.md)

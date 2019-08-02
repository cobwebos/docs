---
title: 用于 IoT 设备的 Azure 安全中心调查指南 |Microsoft Docs
description: 本操作方法指南介绍了如何使用 Azure 安全中心进行 IoT 来调查可疑 IoT 设备的使用 Log Analytics。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596249"
---
# <a name="investigate-a-suspicious-iot-device"></a>调查可疑的 IoT 设备

适用于 IoT 服务警报的 Azure 安全中心可在 IoT 设备涉嫌参与可疑活动时, 或在存在设备受损的迹象时提供清晰的指示。 

在本指南中, 请使用提供的调查建议来帮助确定组织的潜在风险, 确定如何修正, 并发现将来阻止类似攻击的最佳方式。  

> [!div class="checklist"]
> * 查找设备数据
> * 使用 kql 查询进行调查


## <a name="how-can-i-access-my-data"></a>如何访问我的数据？

默认情况下, 适用于 IoT 的 Azure 安全中心在 Log Analytics 工作区中存储安全警报和建议。 你还可以选择存储原始安全数据。

查找数据存储 Log Analytics 工作区:

1. 打开 IoT 中心， 
1. 在 "**安全**" 下, 单击 "**概述**", 然后选择 "**设置**"。
1. 更改 Log Analytics 工作区配置详细信息。 
1. 单击“保存”。 

根据配置执行以下操作，以访问 Log Analytics 工作区中存储的数据：

1. 选择并单击 IoT 中心内的 Azure 安全中心的 IoT 警报。 
1. 单击“进一步调查”。 
1. 选择“若要查看哪些设备生成了此警报，请单击此处并查看 DeviceId 列”。

## <a name="investigation-steps-for-suspicious-iot-devices"></a>可疑 IoT 设备的调查步骤

若要查看有关 IoT 设备的见解和原始数据, 请访问 Log Analytics 工作区[以访问数据](#how-can-i-access-my-data)。

请参阅下面的示例 kql 查询, 开始调查设备上的警报和活动。

### <a name="related-alerts"></a>相关警报

若要确定在大致相同的时间是否触发了其他警报，请使用以下 kql 查询：

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>具有访问权限的用户

若要确定哪些用户有权访问此设备，请使用以下 kql 查询： 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
使用此数据来确定： 
- 哪些用户有权访问该设备？
- 具有访问权限的用户是否具有所需的权限级别？

### <a name="open-ports"></a>打开端口

若要查明设备中当前正在使用或使用的端口, 请使用以下 kql 查询: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

使用此数据来确定：
- 哪些侦听套接字当前在设备上处于活动状态？
- 是否应允许当前处于活动状态的侦听套接字？
- 是否存在连接到设备的可疑远程地址？

### <a name="user-logins"></a>用户登录

若要查找登录到设备的用户, 请使用以下 kql 查询: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

使用查询结果来确定：
- 哪些用户登录到了设备？
- 登录的用户是否应该登录？
- 登录的用户是从预期还是意外的 IP 地址连接的？
  
### <a name="process-list"></a>进程列表

若要查看进程列表是否按预期运行, 请使用以下 kql 查询: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

使用查询结果来确定：

- 是否有任何可疑的进程在设备上运行？
- 进程是否由适当的用户执行？
- 所有命令行执行是否包含正确的预期参数？

## <a name="next-steps"></a>后续步骤

调查设备并更好地了解风险后，可能需要考虑[配置自定义警报](quickstart-create-custom-alerts.md)，以改善 IoT 解决方案的安全态势。 如果没有设备代理，请考虑[部署安全代理](how-to-deploy-agent.md)或[更改现有设备代理的配置](how-to-agent-configuration.md)，以改善结果。 

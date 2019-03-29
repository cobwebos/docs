---
title: 有关 IoT 设备调查指南预览 ASC |Microsoft Docs
description: 此方法指南说明如何使用适用于 IoT 的 ASC 调查可疑使用 Log Analytics 在 IoT 设备。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 032315d1b618b9716e23c8433c6ec1bf64b7e77d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580533"
---
# <a name="investigate-a-suspicious-iot-device"></a>调查可疑的 IoT 设备

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版本没有附带服务级别协议提供，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IoT 服务警报和证据 ASC 提供明确的指示时涉及的可疑活动或迹象存在设备受到怀疑 IoT 设备。 

在本指南中，使用调查建议可以帮助确定你的组织可能面临的风险，决定如何修正并发现在将来防止类似攻击的最佳方法。  

> [!div class="checklist"]
> * 查找设备数据
> * 调查使用 kql 查询


## <a name="how-can-i-access-my-data"></a>如何访问我的数据？

默认情况下，iot ASC 在 Log Analytics 工作区中存储安全警报和建议。 您还可以选择将原始安全数据存储。

若要查找的数据存储在 Log Analytics 工作区：

1. 打开 IoT 中心， 
1. 单击**安全**，然后选择**设置**。
1. 更改你的 Log Analytics 工作区配置详细信息。 
1. 单击“ **保存**”。 

以下配置，执行以下操作来访问数据存储在 Log Analytics 工作区：

1. 选择并单击通知 IoT 中心，IoT ASC。 
1. 单击**进一步调查**。 
1. 选择**若要查看哪些设备具有此警报，请单击此处，查看 DeviceId 列**。

## <a name="investigation-steps-for-suspicious-iot-devices"></a>调查可疑的 IoT 设备的步骤

若要访问的见解和有关 IoT 设备的原始数据，请转到 Log Analytics 工作区[来访问你的数据](#how-can-i-access-my-data)。

检查并调查以下详细信息和活动使用以下 kql 查询的设备数据。

### <a name="related-alerts"></a>相关的警报

若要查找其他警报，是否已触发时间使用同一个围绕以下 kql 查询：

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>具有访问权限的用户

若要找出哪些用户有权访问此设备，请使用以下 kql 查询： 

  ~~~
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
  ~~~
使用此数据来发现： 
  1. 哪些用户有权访问该设备？
  2. 具有访问权限的用户是否按预期的权限级别？ 

### <a name="open-ports"></a>打开端口

若要查找设备中的哪些端口正在使用或使用了的扩展，请使用以下 kql 查询： 

  ~~~
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
  ~~~

    Use this data to discover:
  1. 哪些侦听套接字是否在设备上当前处于活动状态？
  2. 应允许当前处于活动状态的侦听套接字
  3. 是否有任何可疑的远程地址连接到设备？

### <a name="user-logins"></a>用户登录名

若要确定登录到设备的用户，请使用以下 kql 查询： 
 
  ~~~
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
  ~~~

    Use the query results to discover:
  1. 哪些用户登录到设备？
  2. 在中记录的用户应该以用户身份登录？
  3. 未登录的用户连接来自正常或异常 IP 地址？
  
### <a name="process-list"></a>进程列表

若要查找的进程列表是否为预期使用以下 kql 查询： 

  ~~~
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
  ~~~

    Use the query results to discover:

  1. 是否有任何可疑的进程，在设备上运行？
  2. 进程是否已执行了适当的用户？
  3. 未执行任何命令行包含正确和预期参数？

## <a name="next-steps"></a>后续步骤
后调查设备，并获得更好地了解您的风险，您可能想要考虑[配置自定义警报](quickstart-create-custom-alerts.md)以提高 IoT 解决方案安全状态。 如果还没有设备代理，请考虑[部署安全代理](how-to-deploy-agent.md)或[更改为现有的设备代理的配置](how-to-agent-configuration.md)以更好的结果。 
---
title: 如何修改的 IoT 预览版 ASC 为 IoT 模块孪生 ASC 中的说明 |Microsoft Docs
description: 了解如何对 IoT 修改 ASC 为 ASC IoT 安全模块孪生。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541942"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>修改 IoT 模块孪生 ASC

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何修改现有的配置**AzureIoTSecurity 模块孪生**现有设备。 

请参阅[创建 IoT 模块 ASC](quickstart-create-security-twin.md)若要了解如何使新设备的新安全模块。  

## <a name="modification-considerations"></a>修改注意事项

> [!IMPORTANT]
> 孪生配置中的每个配置重写默认配置。 如果特定的配置已不再存在于孪生配置，则使用默认配置。 

## <a name="configuration-schema-and-validation"></a>配置架构和验证 

请确保用于验证对此代理配置[架构](https://github.com/Azure/asc-for-iot/schema/security_module_twin)。 如果配置对象与架构不匹配，将不会启动代理。

 
如果在运行时代理，配置对象更改为非有效的配置 （配置与架构不匹配），该代理将忽略无效的配置，并将继续使用当前配置。 

## <a name="edit-a-property"></a>编辑属性  

设置 AzureIoTSecurity 模块孪生中的代理配置对象内的所有自定义属性。 

若要设置的属性，添加到与所需的值的配置对象的属性键。 若要使用默认属性值，请从配置对象中移除属性：

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>属性 
下表包含所有 IoT 代理控制 ASC 的可配置属性。 
          

| 名称| 状态 | 有效值| 默认值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|所需： false |有效值：格式为 ISO 8601 持续时间 |默认值：PT7M |发送高优先级的消息之前的最大时间。|
|lowPriorityMessageFrequency |所需： false|有效值：格式为 ISO 8601 持续时间 |默认值：PT5H |发送低优先级的消息之前的最大时间。| 
|snapshotFrequency |要求： false|无效值： 的持续时间以 ISO 8601 格式 |默认值 PT13H |设备状态快照的创建的时间间隔。| 
|maxLocalCacheSizeInBytes |所需： false |有效值： |默认值：2560000，大于 8192 | （以字节为单位） 的代理的消息缓存允许的最大存储。 最大允许在设备上存储消息，发送消息前的空间量。| 
|maxMessageSizeInBytes |所需： false |有效值：大于 8192，小于 262144 的正数值 |默认值：204800 |允许的最大大小的代理向云的消息。 此设置控制每个消息中发送的最大数据的量。 |
|eventPriority${EventName} |所需： false |有效值：最高价、 最低价，关闭 |默认值： |每个代理的优先级生成事件。 | 
|

### <a name="events"></a>活动

以下事件列表是所有事件 IoT 代理 ASC 可以从你的设备中收集。 使用 AzureIotSecurity 模块孪生来配置这些事件收集并决定它们在解决方案中的优先级别。 
 
|事件名称| PropertyName | 默认值| 快照事件| 详细状态  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|诊断事件|eventPriorityDiagnostic| 关闭| 假| 代理相关的诊断事件。 使用此事件的详细日志记录。| 
配置错误 |eventPriorityConfigurationError |低 |假 |代理无法分析配置。 验证针对架构的配置。| 
|已删除的事件的统计信息 |eventPriorityDroppedEventsStatistics |低 |真|代理与相关的事件的统计信息。 |
|消息的统计信息|eventPriorityMessageStatistics |低 |真 |代理与相关的消息的统计信息。 |
|已连接的硬件|eventPriorityConnectedHardware |低 |真 |快照的所有硬件连接到设备。|
|侦听端口|eventPriorityListeningPorts |高 |真 |在设备上的所有打开侦听端口的快照。|
| 进程创建 |eventPriorityProcessCreate |低 |假 |审核进程创建在设备上。|
| 进程终止|eventPriorityProcessTerminate |低 |假 |审核进程终止在设备上。| 
 系统信息 |eventPrioritySystemInformation |低 |真 |例如，操作系统或 CPU 的系统信息的快照。|
|本地用户| eventPriorityLocalUsers |高 |真|在系统中已注册的本地用户的快照。 |
|登录|  eventPriorityLogin |高|假|审核到设备 （本地和远程登录名） 的登录事件。|
|创建连接 |eventPriorityConnectionCreate|低|假|创建与设备的 TCP 连接会进行审核。 |
|防火墙配置| eventPriorityFirewallConfiguration|低|真|设备防火墙配置 （防火墙规则） 的快照。 |
|OS 基线| eventPriorityOSBaseline| 低|真|检查设备 OS 基线快照。| 
|


## <a name="next-steps"></a>后续步骤

- 适用于 IoT 读取 ASC[概述](overview.md)
- 了解适用于 IoT ASC[体系结构](architecture.md)
- 了解和探索[ASC 为 IoT 警报](concept-security-alerts.md)
- 了解如何访问你[安全数据](how-to-security-data-access.md)

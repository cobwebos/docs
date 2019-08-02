---
title: 为 IoT 代理配置 Azure 安全中心 |Microsoft Docs
description: 了解如何配置用于 IoT 的 Azure 安全中心的代理。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 8b4764d855663325b2445f7b588b795c15f4edde
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596337"
---
# <a name="tutorial-configure-security-agents"></a>教程：配置安全代理

本文介绍了适用于 IoT 安全代理的 Azure 安全中心, 并详细说明了如何更改和配置它们。 

> [!div class="checklist"]
> * 配置安全代理
> * 通过编辑克隆属性更改代理行为
> * 发现默认配置

## <a name="agents"></a>代理

用于 IoT 安全代理的 Azure 安全中心从 IoT 设备收集数据, 并执行安全操作来缓解检测到的漏洞。 安全代理配置可使用一组可自定义的模块克隆属性进行控制。 通常情况下, 对这些属性的次要更新很少发生。  

用于 IoT 的安全代理克隆的 Azure 安全中心配置对象是一个 JSON 格式对象。 配置对象是一组可控制属性, 您可以定义这些属性来控制代理的行为。 

这些配置有助于为所需的每个方案自定义代理。 例如, 可以通过配置这些属性自动排除某些事件, 或将能耗保持在最低级别。  

使用 Azure 安全中心的 IoT security agent 配置[架构](https://aka.ms/iot-security-github-module-schema)进行更改。  

## <a name="configuration-objects"></a>配置对象 

与 IoT 安全代理的每个 Azure 安全中心相关的属性位于**azureiotsecurity**模块的 "所需属性" 部分中的 "代理配置" 对象中。 

若要修改配置, 请在**azureiotsecurity**模块克隆标识中创建并修改此对象。 

如果**azureiotsecurity**模块中不存在代理配置对象, 则所有安全代理属性值将设置为默认值。 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>配置架构和验证 

请确保针对此[架构](https://aka.ms/iot-security-github-module-schema)验证你的代理配置。 如果配置对象与架构不匹配, 则代理将不会启动。

 
如果在代理运行时将配置对象更改为无效的配置 (该配置与架构不匹配), 则代理将忽略无效的配置, 并将继续使用当前配置。 

### <a name="configuration-validation"></a>配置验证

用于 IoT security agent 的 Azure 安全中心在**azureiotsecurity**模块克隆标识的报告属性部分中报告其当前配置。
代理会报告所有可用属性, 如果用户未设置属性, 则代理将报告默认配置。

若要验证您的配置, 请将所需的部分中设置的值与报告的部分中所报告的值进行比较。

如果所需的属性和报告的属性不匹配, 则代理无法分析配置。

请根据[架构](https://aka.ms/iot-security-github-module-schema)验证所需属性, 修复错误并再次设置所需的属性!

> [!NOTE]
> 如果代理无法分析所需的配置, 则会从代理触发配置错误警报。
> 比较报告的和所需的部分, 了解是否仍适用此警报

## <a name="editing-a-property"></a>编辑属性 

所有自定义属性都必须设置在**azureiotsecurity**模块内的代理配置对象内。
若要使用默认属性值, 请从配置对象中删除属性。

### <a name="setting-a-property"></a>设置属性

1. 在 IoT 中心中, 找到并选择要更改的设备。

2. 单击你的设备, 然后单击 " **azureiotsecurity** " 模块。

3. 单击 "**模块标识符**"。

4. 在 "安全" 模块中编辑要更改的属性。
   
   例如, 若要将连接事件配置为高优先级, 并每隔7分钟收集高优先级事件, 请使用以下配置。
   
   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "highPriorityMessageFrequency": {
          "value" : "PT7M"
        },    
        "eventPriorityConnectionCreate": {
          "value" : "High" 
        }
      } 
    }, 
    ```

5. 单击“保存”。

### <a name="using-a-default-value"></a>使用默认值

若要使用默认属性值, 请从配置对象中删除属性。

## <a name="default-properties"></a>默认属性 

下表包含 IoT security agent 的 Azure 安全中心的可控属性。

[GitHub](https\://aka.ms/iot-security-module-default)中的正确架构中提供了默认值。

| 名称| 状态 | 有效值| 默认值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|必需: false |有效值：ISO 8601 格式的持续时间 |默认值：PT7M |发送高优先级消息之前的最大时间间隔。|
|lowPriorityMessageFrequency |必需: false|有效值：ISO 8601 格式的持续时间 |默认值：PT5H |发送低优先级邮件前的最长时间。| 
|snapshotFrequency |要求: false|有效值：ISO 8601 格式的持续时间 |默认值 PT13H |创建设备状态快照的时间间隔。| 
|maxLocalCacheSizeInBytes |必需: false |有效值： |默认值：2560000, 大于8192 | 代理的消息缓存允许的最大存储空间 (以字节为单位)。 发送消息之前, 允许在设备上存储消息的最大空间量。| 
|maxMessageSizeInBytes |必需: false |有效值：正数, 大于 8192, 小于262144 |默认值：204800 |代理到云消息的最大允许大小。 此设置控制每条消息中发送的最大数据量。 |
|eventPriority${EventName} |必需: false |有效值：高、低、关 |默认值: |每个代理生成的事件的优先级 | 

### <a name="supported-security-events"></a>支持的安全事件

|事件名称| PropertyName | Default Value| Snapshot 事件| 详细信息状态  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|诊断事件|eventPriorityDiagnostic| 关| False| 与代理相关的诊断事件。 使用此事件进行详细日志记录。| 
|配置错误 |eventPriorityConfigurationError |低 |False |代理无法分析配置。 根据架构验证配置。| 
|删除的事件统计信息 |eventPriorityDroppedEventsStatistics |低 |True|与代理相关的事件统计信息。 |
|消息统计信息|eventPriorityMessageStatistics |低 |True |与代理相关的消息统计信息。 |
|已连接硬件|eventPriorityConnectedHardware |低 |True |连接到设备的所有硬件的快照。|
|侦听端口|eventPriorityListeningPorts |高 |True |设备上所有打开的侦听端口的快照。|
|进程创建 |eventPriorityProcessCreate |低 |False |审核在设备上的进程创建。|
|进程终止|eventPriorityProcessTerminate |低 |False |审核在设备上终止进程。| 
|系统信息 |eventPrioritySystemInformation |低 |True |系统信息的快照 (例如:操作系统或 CPU)。| 
|本地用户| eventPriorityLocalUsers |高 |True|系统中已注册的本地用户的快照。 |
|登录|  eventPriorityLogin |高|False|审核登录事件到设备 (本地和远程登录)。|
|连接创建 |eventPriorityConnectionCreate|低|False|审核在设备上创建的 TCP 连接。 |
|防火墙配置| eventPriorityFirewallConfiguration|低|True|设备防火墙配置的快照 (防火墙规则)。 |
|操作系统基线| eventPriorityOSBaseline| 低|True|设备操作系统基线检查的快照。|
|
 

## <a name="next-steps"></a>后续步骤

- [了解 Azure 安全中心的 IoT 建议](concept-recommendations.md)
- [了解 Azure 安全中心的 IoT 警报](concept-security-alerts.md)
- [访问原始安全数据](how-to-security-data-access.md)

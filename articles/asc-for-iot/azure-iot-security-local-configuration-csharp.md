---
title: 了解适用于C# IoT 安全中心的 Azure 安全中心的本地配置文件 |Microsoft Docs
description: 详细了解适用于 IoT 安全服务的 Azure 安全中心和的C#安全代理本地配置文件。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 0172ada68ffa652fb0c301c89238beca4f4ce2f9
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664180"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>了解本地配置文件（C#代理）


用于 IoT security agent 的 Azure 安全中心使用本地配置文件中的配置。

当代理启动时，安全代理读取一次配置文件。 本地配置文件中的配置包含身份验证配置和其他代理相关配置。

C#安全代理使用多个配置文件：

- **常规 .Config**代理相关配置。
- **身份验证**与身份验证相关的配置（包括身份验证详细信息）。
- **SecurityIotInterface** -IoT 相关配置。

配置文件包含默认配置。 身份验证配置在代理安装过程中进行填充，并且在重新启动代理时对配置文件进行更改。 

## <a name="configuration-file-location"></a>配置文件位置
对于 Linux：
- 操作系统配置文件位于 `/var/ASCIoTAgent`。

对于 Windows：
- 操作系统配置文件位于安全代理的目录中。 

### <a name="generalconfig-configurations"></a>常规 .config 配置

| 配置名称 | 可能的值 | 详细信息 | 
|:-----------|:---------------|:--------|
| agentId | GUID | 代理唯一标识符 |
| readRemoteConfigurationTimeout | TimeSpan | 从 IoT 中心获取远程配置的时间段。 如果代理无法在指定的时间内提取配置，操作将超时。|
| schedulerInterval | TimeSpan | 内部计划程序间隔。 |
| producerInterval | TimeSpan | 事件发生器辅助角色间隔。 |
| consumerInterval | TimeSpan | 事件使用者辅助角色间隔。 |
| highPriorityQueueSizePercentage | 0 < 号 < 1 | 高优先级消息专用的总缓存部分。 |
| logLevel | "Off"、"严重"、"错误"、"警告"、"信息"、"调试"  | 日志消息等于或高于此严重级别会记录到调试控制台（Linux 中的 Syslog）。 |
| fileLogLevel |  "Off"、"严重"、"错误"、"警告"、"信息"、"调试"| 日志消息等于或高于此严重级别会记录到文件（Linux 中的 Syslog）。 |
| diagnosticVerbosityLevel | "无"、"部分"、"全部"、 | 诊断事件的详细级别。 无-不发送诊断事件，发送仅包含高重要性的部分诊断事件，所有-所有日志也作为诊断事件发送。 |
| logFilePath | 文件路径 | 如果 fileLogLevel > 关闭，则日志将写入此文件。 |
| defaultEventPriority | "高"、"低"、"关" | 默认事件优先级。 |

### <a name="generalconfig-example"></a>常规 .config 示例
```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>身份验证 .config

| 配置名称 | 可能的值 | 详细信息 | 
|:-----------|:---------------|:--------|
| moduleName | 字符串 | 安全模块标识的名称。 此名称必须与设备中的模块标识名称相对应。 |
| deviceId | 字符串 | 设备的 ID （在 Azure IoT 中心内注册）。 || schedulerInterval | TimeSpan 字符串 | 内部计划程序间隔。 |
| gatewayHostname | 字符串 | Azure Iot 中心的主机名。 通常 < 集线器 >。 devices.net |
| filePath | 字符串-文件路径 | 包含身份验证机密的文件的路径。|
| type | "SymmetricKey"、"New-selfsignedcertificate" | 用于身份验证的用户机密。 如果用户机密是对称密钥，请选择 " *SymmetricKey* "，如果密码是自签名证书，请选择 "*自签名证书*"。 |
| 标识 | "DPS"、"Module"、"Device" | 身份验证标识-DPS 如果通过 DPS、Module 进行身份验证，则使用模块凭据进行身份验证，或者在使用设备凭据进行身份验证时使用设备。
| certificateLocationKind |  "LocalFile"、"Store" | LocalFile 如果证书存储在文件中，则存储证书是否位于证书存储区中。 |
| idScope | 字符串 | DPS 的 ID 范围 |
| registrationId | 字符串  | DPS 设备注册 ID。 |
|

### <a name="authenticationconfig-example"></a>Authentication .config 示例
```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```
### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface

| 配置名称 | 可能的值 | 详细信息 | 
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | IoT 中心传输类型。 |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface 示例
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>后续步骤
- 阅读 Azure 安全中心以获取 IoT 服务[概述](overview.md)
- 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 为 IoT[服务](quickstart-onboard-iot-hub.md)启用 Azure 安全中心
- 阅读 Azure 安全中心以获取 IoT 服务[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
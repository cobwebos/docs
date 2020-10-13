---
title: 'Defender for IoT security agent 本地配置 (c # ) '
description: '详细了解适用于适用于 c # 的适用于 IoT 的安全服务安全代理本地配置文件。'
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 13c16407481d4fa6f7d468a73051cc4945e6314e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851227"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>了解 c # 代理 (的本地配置文件) 

用于 IoT security agent 的 Defender 使用本地配置文件中的配置。

当代理开始运行时，安全代理读取一次配置文件。 本地配置文件中的配置包含身份验证配置和其他代理相关配置。

C # 安全代理使用多个配置文件：

- **General.config** 代理相关配置。
- **Authentication.config** 身份验证相关配置 (包括) 身份验证详细信息。
- 与**SecurityIotInterface.config** IoT 相关的配置。

配置文件包含默认配置。 身份验证配置在代理安装过程中进行填充，并且在重新启动代理时对配置文件进行更改。

## <a name="configuration-file-location"></a>配置文件位置

对于 Linux：

- 操作系统配置文件位于中 `/var/ASCIoTAgent` 。

对于 Windows：

- 操作系统配置文件位于安全代理的目录中。

### <a name="generalconfig-configurations"></a>General.config 配置

| 配置名称 | 可能的值 | 详细信息 |
|:-----------|:---------------|:--------|
| agentId | GUID | 代理唯一标识符 |
| readRemoteConfigurationTimeout | TimeSpan | 从 IoT 中心获取远程配置的时间段。 如果代理无法在指定的时间内提取配置，操作将超时。|
| schedulerInterval | TimeSpan | 内部计划程序间隔。 |
| producerInterval | TimeSpan | 事件发生器辅助角色间隔。 |
| consumerInterval | TimeSpan | 事件使用者辅助角色间隔。 |
| highPriorityQueueSizePercentage | 0 < 号 < 1 | 高优先级消息专用的总缓存部分。 |
| logLevel | "Off"、"严重"、"错误"、"警告"、"信息"、"调试"  | 日志消息等于或高于此严重性，将记录到 Linux) 中的调试控制台 (Syslog。 |
| fileLogLevel |  "Off"、"严重"、"错误"、"警告"、"信息"、"调试"| 日志消息等于或高于此严重性，会记录到 Linux) 中的文件 (Syslog。 |
| diagnosticVerbosityLevel | "无"、"部分"、"全部"、 | 诊断事件的详细级别。 无-不发送诊断事件。 发送了重要性较高的部分仅有的诊断事件。 所有-所有日志也作为诊断事件发送。 |
| logFilePath | 文件路径 | 如果 fileLogLevel > 关闭，则日志将写入此文件。 |
| defaultEventPriority | "高"、"低"、"关" | 默认事件优先级。 |

### <a name="generalconfig-example"></a>General.config 示例

```xml
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

### <a name="authenticationconfig"></a>Authentication.config

| 配置名称 | 可能的值 | 详细信息 |
|:-----------|:---------------|:--------|
| moduleName | 字符串 | 安全模块标识的名称。 此名称必须与设备中的模块标识名称相对应。 |
| deviceId | string | 在 Azure IoT 中心) 中注册的设备 ID (。 |
| schedulerInterval | TimeSpan 字符串 | 内部计划程序间隔。 |
| gatewayHostname | 字符串 | Azure Iot 中心的主机名。 通常 <我的中心>。 azure-devices.net |
| filePath | 字符串-文件路径 | 包含身份验证机密的文件的路径。|
| type | "SymmetricKey"、"New-selfsignedcertificate" | 用于身份验证的用户机密。 如果用户机密是对称密钥，请选择 " *SymmetricKey* "，如果密码是自签名证书，请选择 " *自签名证书* "。 |
| 标识 | "DPS"、"Module"、"Device" | 身份验证标识-DPS 如果通过 DPS、Module 进行身份验证，则使用模块凭据进行身份验证，或者在使用设备凭据进行身份验证时使用设备。
| certificateLocationKind |  "LocalFile"、"Store" | LocalFile 如果证书存储在文件中，则存储证书是否位于证书存储区中。 |
| idScope | 字符串 | DPS 的 ID 范围 |
| registrationId | 字符串  | DPS 设备注册 ID。 |
|

### <a name="authenticationconfig-example"></a>Authentication.config 示例

```xml
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

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| 配置名称 | 可能的值 | 详细信息 |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | IoT 中心传输类型。 |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config 示例

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>后续步骤

- 阅读用于 IoT 服务的 Defender [概述](overview.md)
- 了解有关用于 IoT[体系结构](architecture.md)的 Defender 的详细信息
- 为 IoT[服务](quickstart-onboard-iot-hub.md)启用 Defender
- 阅读用于 IoT 服务的 Defender [常见问题](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全 [警报](concept-security-alerts.md)

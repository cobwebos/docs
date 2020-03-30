---
title: 了解 Azure 安全中心，用于 IoT 安全代理本地配置文件，用于 C# |微软文档
description: 详细了解适用于 IoT 安全服务的 Azure 安全中心、C# 的安全代理本地配置文件。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664180"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>了解本地配置文件 （C# 代理）


IoT 安全代理的 Azure 安全中心使用本地配置文件中的配置。

当代理启动时，安全代理读取配置文件一次。 在本地配置文件中找到的配置同时包含身份验证配置和其他代理相关配置。

C# 安全代理使用多个配置文件：

- **常规.配置**- 代理相关配置。
- **身份验证.config** - 身份验证相关配置（包括身份验证详细信息）。
- **安全 Iot 接口.配置**- IoT 相关配置。

配置文件包含默认配置。 身份验证配置在代理安装期间填充，并在重新启动代理时对配置文件进行更改。 

## <a name="configuration-file-location"></a>配置文件位置
对于 Linux：
- 操作系统配置文件位于`/var/ASCIoTAgent`中。

对于 Windows：
- 操作系统配置文件位于安全代理的目录中。 

### <a name="generalconfig-configurations"></a>常规.配置配置

| 配置名称 | 可能值 | 详细信息 | 
|:-----------|:---------------|:--------|
| 代理 Id | GUID | 代理唯一标识符 |
| 读取远程配置超时 | TimeSpan | 从 IoT 中心获取远程配置的时间段。 如果代理无法在指定时间内获取配置，则操作将超时。|
| 计划时间间隔 | TimeSpan | 内部计划程序间隔。 |
| 生产者间隔 | TimeSpan | 事件生成器辅助角色间隔。 |
| 消费者间隔 | TimeSpan | 事件使用者辅助角色间隔。 |
| 高优先级队列大小百分比 | 0 < 数字 < 1 | 专用于高优先级消息的总缓存部分。 |
| logLevel | "关闭"、"致命"、"错误"、"警告"、"信息"、"调试"  | 将等于且高于此严重性的错误日志记录到调试控制台（Linux 中的 Syslog）。 |
| 文件日志级别 |  "关闭"、"致命"、"错误"、"警告"、"信息"、"调试"| 相同且高于此严重性的日志消息将记录到文件中（Linux 中的 Syslog）。 |
| 诊断详细级别 | "无"，"一些"，"所有" | 诊断事件的风险级别。 无 - 未发送诊断事件，某些 - 仅发送具有高重要性的诊断事件，所有 - 所有日志也作为诊断事件发送。 |
| 日志文件路径 | 文件路径 | 如果 fileLogLevel >关闭，日志将写入此文件。 |
| 默认事件优先级 | "高"，"低"，"关" | 默认事件优先级。 |

### <a name="generalconfig-example"></a>常规.config 示例
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

### <a name="authenticationconfig"></a>身份验证. 配置

| 配置名称 | 可能值 | 详细信息 | 
|:-----------|:---------------|:--------|
| moduleName | 字符串 | 安全模块标识的名称。 此名称必须与设备中的模块标识名称对应。 |
| deviceId | 字符串 | 设备的 ID（在 Azure IoT 中心注册）。 || 计划时间间隔 | 时间跨度字符串 | 内部计划程序间隔。 |
| 网关主机名 | 字符串 | Azure Iot 中心的主机名。 通常<我的集线器>.azure-devices.net |
| filePath | 字符串 - 文件路径 | 包含身份验证密钥的文件的路径。|
| type | "对称密钥"，"自签名证书" | 用于身份验证的用户密钥。 如果用户密钥是对称密钥，请选择*SymmetricKey，* 如果密码是自签名证书，请选择*自签名证书*。 |
| identity | "DPS"、"模块"、"设备" | 身份验证标识 - 通过 DPS 进行身份验证的 DPS、使用模块凭据进行身份验证的模块或使用设备凭据进行身份验证的设备。
| 证书位置金德 |  "本地文件"，"存储" | 如果证书存储在文件中，则本地文件，如果证书位于证书存储中，则存储。 |
| idScope | 字符串 | DPS 的 ID 范围 |
| 注册 Id | 字符串  | DPS 设备注册 ID。 |
|

### <a name="authenticationconfig-example"></a>身份验证.config 示例
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
### <a name="securityiotinterfaceconfig"></a>安全Iot接口.配置

| 配置名称 | 可能值 | 详细信息 | 
|:-----------|:---------------|:--------|
| transportType | "安普克""Mqtt" | IoT 集线器传输类型。 |
|

### <a name="securityiotinterfaceconfig-example"></a>安全Iot接口.config示例
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>后续步骤
- 阅读 Azure 安全中心，了解 IoT 服务[概述](overview.md)
- 了解有关 IoT[体系结构](architecture.md)的 Azure 安全中心
- 启用适用于 IoT[服务的](quickstart-onboard-iot-hub.md)Azure 安全中心
- 阅读 Azure 安全中心，了解 IoT 服务[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
---
title: 安全代理身份验证方法
description: 了解在使用用于 IoT 的 Defender 服务时可用的不同身份验证方法。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 896baa70fb6e18165a025459a063e33d705261ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934469"
---
# <a name="security-agent-authentication-methods"></a>安全代理身份验证方法

本文介绍了可与 AzureIoTSecurity agent 一起用于向 IoT 中心进行身份验证的不同身份验证方法。

对于 IoT 中心内的每个设备载入到 Defender for IoT，需要一个安全模块。 要对设备进行身份验证，IoT 的 Defender 可以使用以下两种方法之一。 选择最适合于现有 IoT 解决方案的方法。

> [!div class="checklist"]
> * SecurityModule 选项
> * 设备选项

## <a name="authentication-methods"></a>身份验证方法

AzureIoTSecurity 代理执行身份验证的两种方法：

- **SecurityModule** 身份验证模式<br>
使用独立于设备标识的安全模块标识对代理进行身份验证。
如果希望安全代理通过安全模块使用专用身份验证方法，只需)  (对称密钥，则可以使用此身份验证类型。

- **设备** 身份验证模式<br>
在此方法中，安全代理首先用设备标识进行身份验证。 初始身份验证完成后，IoT 代理的 Defender 使用 REST API 设备的身份验证数据对 IoT 中心执行 **REST** 调用。 然后，用于 IoT 代理的 Defender 将从 IoT 中心请求安全模块身份验证方法和数据。 在最后一步中，IoT 代理的 Defender 对 IoT 模块的 Defender 执行身份验证。

如果希望安全代理重复使用现有设备身份验证方法 (自签名证书或对称密钥) ，请使用此身份验证类型。

有关如何配置的详细说明，请参阅 [安全代理安装参数](#security-agent-installation-parameters) 。

## <a name="authentication-methods-known-limitations"></a>身份验证方法已知限制

- **SecurityModule** authentication 模式仅支持对称密钥身份验证。
- **设备**身份验证模式不支持 CA-Signed 证书。

## <a name="security-agent-installation-parameters"></a>安全代理安装参数

[部署安全代理](how-to-deploy-agent.md)时，必须以参数形式提供身份验证详细信息。
下表中介绍了这些参数。

|Linux 参数名称 | Windows 参数名称 | 速记参数 |说明|选项|
|---------------------|---------------|---------|---------------|---------------|
|身份验证-标识|AuthenticationIdentity|aui|身份验证标识| **SecurityModule** 或 **设备**|
|authentication-method|AuthenticationMethod|aum|身份验证方法|**SymmetricKey** 或 **new-selfsignedcertificate**|
|文件路径|文件路径|F|包含证书或对称密钥的文件的绝对完整路径| |
|主机名|HostName|hn|IoT 中心的 FQDN|示例： ContosoIotHub.azure-devices.net|
|设备 id|DeviceId|di|设备 ID|示例： MyDevice1|
|证书-位置种类|CertificateLocationKind|cl|证书存储位置|**LocalFile** 或 **存储**|
|

使用安装安全代理脚本时，会自动执行以下配置。 若要手动编辑安全代理身份验证，请编辑配置文件。

## <a name="change-authentication-method-after-deployment"></a>部署后更改身份验证方法

使用安装脚本部署安全代理时，系统会自动创建一个配置文件。

若要在部署后更改身份验证方法，需要手动编辑配置文件。

### <a name="c-based-security-agent"></a>基于 c # 的安全代理

用以下参数编辑 _Authentication.config_ ：

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>基于 C 的安全代理

用以下参数编辑 _LocalConfiguration.js_ ：

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>另请参阅

- [安全代理概述](security-agent-architecture.md)
- [部署安全代理](how-to-deploy-agent.md)
- [访问原始安全数据](how-to-security-data-access.md)

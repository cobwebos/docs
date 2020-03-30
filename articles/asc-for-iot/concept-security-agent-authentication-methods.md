---
title: IoT® Azure 安全中心的身份验证方法*微软文档
description: 了解使用 Azure IoT 服务安全中心时可用的不同身份验证方法。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596468"
---
# <a name="security-agent-authentication-methods"></a>安全代理身份验证方法 

本文介绍了与 AzureIoT 安全代理一起使用的不同身份验证方法，以便对 IoT 中心进行身份验证。

对于在 IoT 中心中连接到 Azure IoT IoT 安全中心的每个设备，都需要一个安全模块。 要对设备进行身份验证，IoT 的 Azure 安全中心可以使用两种方法之一。 选择最适合现有 IoT 解决方案的方法。 

> [!div class="checklist"]
> * 安全模块选项
> * 设备选项

## <a name="authentication-methods"></a>身份验证方法

AzureIoT 安全代理执行身份验证的两种方法：

 - **安全模块**身份验证模式<br>
   代理使用独立于设备标识的安全模块标识进行身份验证。
   如果您希望安全代理通过安全模块（仅限对称密钥）使用专用身份验证方法，请使用此身份验证类型。
        
 - **设备**身份验证模式<br>
    在此方法中，安全代理首先使用设备标识进行身份验证。 初始身份验证后，IoT 代理的 Azure 安全中心使用 REST API 对 IoT 中心执行**REST**调用，并带有设备的身份验证数据。 然后，IoT 代理的 Azure 安全中心从 IoT 中心请求安全模块身份验证方法和数据。 在最后一步中，IoT 代理的 Azure 安全中心对 IoT 模块的 Azure 安全中心执行身份验证。
    
    如果希望安全代理重用现有设备身份验证方法（自签名证书或对称密钥），请使用此身份验证类型。 

请参阅[安全代理安装参数](#security-agent-installation-parameters)，了解如何配置。
                                
## <a name="authentication-methods-known-limitations"></a>身份验证方法已知限制

- **安全模块**身份验证模式仅支持对称密钥身份验证。
- **设备**身份验证模式不支持 CA 签名证书。  

## <a name="security-agent-installation-parameters"></a>安全代理安装参数

[部署安全代理时](how-to-deploy-agent.md)，身份验证详细信息必须作为参数提供。
下表中记录了这些参数。


|Linux 参数名称 | 窗口参数名称 | 速记参数 |描述|选项|
|---------------------|---------------|---------|---------------|---------------|
|身份验证标识|身份验证标识|艾伊|身份验证标识| **安全模块**或**设备**|
|authentication-method|身份验证方法|阿姆|身份验证方法|**对称密钥**或**自签名证书**|
|文件路径|文件路径|f|包含证书或对称键的文件的绝对完整路径| |
|主机名|HostName|hn|物联网中心 FQDN|示例：ContosoIotHub.azure-devices.net|
|设备 id|DeviceId|地|设备 ID|示例：我的设备1|
|证书位置类型|证书位置金德|Cl|证书存储位置|**本地文件**或**存储**|
|


使用安装安全代理脚本时，将自动执行以下配置。 要手动编辑安全代理身份验证，请编辑配置文件。 

## <a name="change-authentication-method-after-deployment"></a>部署后更改身份验证方法

使用安装脚本部署安全代理时，将自动创建配置文件。

要在部署后更改身份验证方法，需要手动编辑配置文件。


### <a name="c-based-security-agent"></a>基于 C# 的安全代理

编辑_身份验证.配置_与以下参数：

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

使用以下参数编辑_本地配置.json：_

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>请参阅
- [安全代理概述](security-agent-architecture.md)
- [部署安全代理](how-to-deploy-agent.md)
- [访问原始安全数据](how-to-security-data-access.md)

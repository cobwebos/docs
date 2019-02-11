---
title: Microsoft Azure Data Box Edge 系统要求 | Microsoft Docs
description: 了解 Azure Data Box Edge 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/14/2019
ms.author: alkohli
ms.openlocfilehash: 60c4b22fb34a66a0ff68db26030be0e0ea3c0066
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470233"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Azure Data Box Edge系统要求（预览版）

本文介绍 Microsoft Azure Data Box Edge 解决方案以及连接到 Azure Data Box Edge 的客户端的重要系统要求。 我们建议在部署 Data Box Edge 之前仔细查看这些信息。 在部署和执行后续操作期间，如有必要，可以回来参考此信息。

Data Box Edge 的系统要求包括：

- **主机的软件要求** - 介绍支持的平台、本地配置 UI 的浏览器、SMB 客户端以及访问设备的客户端的任何其他要求。
- **设备的网络要求** - 提供有关物理设备运转的网络要求的信息。

> [!IMPORTANT]
> Data Box Edge 以预览版提供。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="supported-os-for-clients-connected-to-device"></a>连接到设备的客户端支持的 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>访问设备的客户端支持的协议

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>支持的存储类型

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>本地 Web UI 支持的浏览器

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="port-configuration-for-data-box-edge"></a>Data Box Edge 的端口配置

下表列出了需要在防火墙中打开以允许 SMB、云或管理流量的端口。 在此表中，*入*或*入站*表示传入客户端请求访问设备的方向。 “传出”或“出站”表示 Data Box Edge 设备从外部（超出部署范围）发送数据的方向：例如，向 Internet 发送出站数据。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>IoT Edge 的端口配置

Azure IoT Edge 允许使用支持的 IoT 中心协议从本地 Edge 设备来与 Azure 云进行出站通信。 仅在 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备（例如，云到设备的消息传送）的特定情况下，才需要进行入站通信。

对托管 Azure IoT Edge 运行时的服务器使用下表中的端口配置：

| 端口号。 | 入或出 | 端口范围 | 必选 | 指南 |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| 出       | WAN        | 是      | IoT Edge 的默认通信协议。 如果未为其他支持的协议配置 Azure IoT Edge，或者 AMQP 是所需的通信协议，则必须打开此端口。 <br>IoT Edge 不支持将端口 5672 用于 AMQP。 <br>当 Azure IoT Edge 使用不同的受 IoT 中心支持的协议时，请阻止此端口。 |
| TCP 443 (HTTPS)| 出       | WAN        | 是      | 为 IoT Edge 预配打开此出站端口。 如果透明网关中的叶设备可能发送方法请求， 则无需向外部网络打开端口 443，即可连接到 IoT 中心或通过 Azure IoT Edge 提供 IoT 中心服务。 因此，传入规则可限制为只能从内部网络打开入站连接。 |
| TCP 5671 (AMQP) | In        |            | 否       | 应阻止入站连接。|
| TCP 443 (HTTPS) | In        |            | 对于某些情况，请参阅注释 | 只应针对特定的情况打开入站连接。 如果无法配置非 HTTP 协议（例如 AMQP、MQTT），可以使用端口 443 通过 WebSocket 发送消息。 |

有关完整信息，请转到 [IoT Edge 部署的防火墙和端口配置规则](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

## <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式

通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 Data Box Edge 设备和服务依赖于其他 Microsoft 应用程序，例如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 这些更改要求网络管理员在需要时为 Data Box Edge 监视和更新防火墙规则。

绝大多数情况下，建议基于 Data Box Edge 固定 IP 地址为出站流量设置防火墙规则。 但是，下面的信息可用于设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> - 设备（源）IP 应始终设置为所有已启用云的网络接口。
> - 目标 IP 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>网关功能的 URL 模式

|    URL 模式                                                                                                                                                                                                                                                                                                                                                                                                                                                      |    组件或功能                                                                           |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                                                                           |    Azure Data Box Edge 服务<br>Azure 服务总线<br>身份验证服务                           |
|    http://\*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                                |    设备激活                                                                                    |
|    http://crl.microsoft.com/pki/\*<br>http://www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    证书吊销                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                            |    Azure 存储帐户和监视                                                                |
|    http://windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Microsoft 更新服务器                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    Akamai CDN                                                                                           |
|    https://\*.partners.extranet.microsoft.com/\*                                                                                                                                                                                                                                                                                                                                                                                                                    |    支持包                                                                                      |
|    http://\*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                                     |    Windows 中的遥测服务，请参阅客户体验和诊断遥测的更新      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                         |


### <a name="url-patterns-for-compute-feature"></a>计算功能的 URL 模式

| URL 模式                      | 组件或功能                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Microsoft 容器注册表（必填）               |   |
| https://\*.azurecr.io                     | 个人和第三方容器注册表（可选） |   |
| https://\*.azure-devices.net              | Iot 中心访问权限（必填）                             |   |

## <a name="internet-bandwidth"></a>Internet 带宽

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>后续步骤

* [部署 Azure Data Box Edge](data-box-Edge-deploy-prep.md)

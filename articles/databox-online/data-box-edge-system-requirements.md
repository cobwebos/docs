---
title: Microsoft Azure Data Box Edge 系统要求 | Microsoft Docs
description: 了解 Azure Data Box Edge 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76757860"
---
# <a name="azure-data-box-edge-system-requirements"></a>Azure Data Box Edge 系统要求

本文介绍 Microsoft Azure Data Box Edge 解决方案以及连接到 Azure Data Box Edge 的客户端的重要系统要求。 我们建议在部署 Data Box Edge 之前仔细查看这些信息。 在部署和执行后续操作期间，如有必要，可以回来参考此信息。

Data Box Edge 的系统要求包括：

- **主机的软件要求** - 介绍支持的平台、本地配置 UI 的浏览器、SMB 客户端以及访问设备的客户端的任何其他要求。
- **设备的网络要求** - 提供有关物理设备运转的网络要求的信息。

## <a name="supported-os-for-clients-connected-to-device"></a>连接到设备的客户端支持的 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>访问设备的客户端支持的协议

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>本地 Web UI 支持的浏览器

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>网络端口要求

### <a name="port-requirements-for-data-box-edge"></a>Data Box Edge 的端口要求

下表列出了需要在防火墙中打开以允许 SMB、云或管理流量的端口。 在此表中，*入*或*入站*表示传入客户端请求访问设备的方向。 “传出”或“出站”表示 Data Box Edge 设备从外部（超出部署范围）发送数据的方向：例如，向 Internet 发送出站数据。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge 的端口要求

Azure IoT Edge 允许使用支持的 IoT 中心协议从本地 Edge 设备来与 Azure 云进行出站通信。 仅在 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备（例如，云到设备的消息传送）的特定情况下，才需要进行入站通信。

对托管 Azure IoT Edge 运行时的服务器使用下表中的端口配置：

| 端口号。 | 入或出 | 端口范围 | 需要 | 指南 |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| 出       | WAN        | 是      | 为 IoT Edge 预配打开此出站端口。 使用手动脚本或 Azure IoT 设备预配服务 (DPS) 时，此配置是必需的。|

有关完整信息，请转到 [IoT Edge 部署的防火墙和端口配置规则](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

## <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式

通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 Data Box Edge 设备和服务依赖于其他 Microsoft 应用程序，例如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 这些更改要求网络管理员在需要时为 Data Box Edge 监视和更新防火墙规则。

绝大多数情况下，建议基于 Data Box Edge 固定 IP 地址为出站流量设置防火墙规则。 但是，下面的信息可用于设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> - 设备（源）IP 应始终设置为所有已启用云的网络接口。
> - 目标 IP 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>网关功能的 URL 模式

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>计算功能的 URL 模式

| URL 模式                      | 组件或功能                     |   
|----------------------------------|---------------------------------------------|
| https：\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 容器注册表（必填）               |
| https://\*.azurecr.io                     | 个人和第三方容器注册表（可选） | 
| https://\*.azure-devices.net              | Iot 中心访问权限（必填）                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>适用于 Azure 政府的网关的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure 政府版计算的 URL 模式

| URL 模式                      | 组件或功能                     |  
|----------------------------------|---------------------------------------------|
| https：\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft 容器注册表（必填）               |
| https://\*.azure-devices.us              | Iot 中心访问权限（必填）           |
| https://\*. azurecr.us                    | 个人和第三方容器注册表（可选） | 

## <a name="internet-bandwidth"></a>Internet 带宽

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>计算大小注意事项

在开发和测试解决方案时使用体验，以确保 Data Box Edge 设备上有足够的容量，并获得设备的最佳性能。

应考虑的因素包括：

- **容器详细信息**-请考虑以下内容。

    - 工作负荷中有多少容器？ 你可能有大量轻型容器，而不是占用大量资源的容器。
    - 分配给这些容器的资源有哪些，以及它们消耗的资源是多少？
    - 容器共享多少层？
    - 是否存在未使用的容器？ 已停止的容器仍占用磁盘空间。
    - 你的容器在哪种语言中编写？
- 已**处理的数据大小**-容器处理的数据量是多少？ 此数据是否会占用磁盘空间，否则数据将在内存中进行处理吗？
- **预期的性能**-你的解决方案所需的性能特征是什么？ 

若要了解和优化解决方案的性能，可以使用：

- Azure 门户中可用的计算指标。 中转到 Data Box Edge 资源，然后开始**监视 > 指标**。 查看**边缘计算内存使用情况**和**边缘计算百分比 CPU** ，了解可用资源以及资源的使用情况。
- 可通过设备的 PowerShell 接口使用的监视命令，例如：

    - `dkrdbe stats` 获取容器资源使用情况统计信息的实时流。 命令支持 CPU、内存使用量、内存限制和网络 IO 指标。
    - `dkrdbe system df` 获取有关使用的磁盘空间量的信息。 
    - `dkrdbe image prune` 清除未使用的映像并释放空间。
    - `dkrdbe ps --size`，查看正在运行的容器的近似大小。 

    有关可用命令的详细信息，请参阅对[计算模块进行监视和故障排除](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)。

最后，请确保在数据集上验证解决方案并量化 Data Box Edge 上的性能，然后再在生产环境中进行部署。


## <a name="next-step"></a>后续步骤

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)

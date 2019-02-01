---
title: Microsoft Azure Data Box Gateway 系统要求 | Microsoft Docs
description: 了解 Azure Data Box Gateway 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 81df0a776cd22490342230567deacb23097cd12e
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094183"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Azure Data Box Gateway 系统要求（预览版）

本文介绍了 Microsoft Azure Data Box Gateway 解决方案以及连接到 Azure Data Box Gateway 的客户端的重要系统要求。 建议在部署 Data Box Gateway 之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

Data Box Gateway 虚拟设备的系统要求包括：

- **主机的软件要求** - 介绍了支持的平台、本地配置 UI 的浏览器、SMB 客户端以及连接到设备的主机的任何其他要求。
- **设备的网络要求** - 提供有关虚拟设备运转的网络要求的信息。

> [!IMPORTANT]
> Data Box Gateway 以预览版提供。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

## <a name="specifications-for-the-virtual-device"></a>虚拟设备的规范

Data Box Gateway 的基础主机系统可以提供以下资源来预配虚拟设备：

| 规范                                          | 说明              |
|---------------------------------------------------------|--------------------------|
| 虚拟处理器（核心数）   | 最低为 4 核 |            
| 内存  | 最低为 8 GB|
| 可用性|单节点|
| 磁盘| OS 磁盘：250 GB <br> 数据磁盘：最低为 2 TB、预配精简，并且必须由 SSD 提供支持|
| 网络接口|1 个或多个虚拟网络接口|


## <a name="supported-os-for-clients-connected-to-device"></a>连接到设备的客户端支持的 OS

下面是连接到 Data Box Gateway 的客户端或主机支持的操作系统的列表。

| **操作系统/平台** | **版本** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 <br> 2019 |
| Windows |8、10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>访问设备的客户端支持的协议

|协议 |**版本**   |**说明**  |
|---------|---------|---------|
|SMB    | 2.X、3.X      | 不支持 SMB 1。|
|NFS     | V3 和 V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>设备支持的虚拟化平台

| **操作系统/平台**  |**版本**   |**说明**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |不支持 VMware 工具。         |


## <a name="supported-storage-accounts"></a>支持的存储帐户

下面是 Data Box Gateway 支持的存储帐户的列表。

| **存储帐户** | **说明** |
| --- | --- |
| 经典 | 标准 |
| 常规用途  |标准；同时支持 V1 和 V2。 同时支持热层和冷层。 |


## <a name="supported-storage-types"></a>支持的存储类型

下面是 Data Box Gateway 支持的存储类型的列表。

| **文件格式** | **说明** |
| --- | --- |
| Azure 块 blob | |
| Azure 页 blob  | |
| Azure 文件 | |

## <a name="supported-browsers-for-local-web-ui"></a>本地 Web UI 支持的浏览器

下面是虚拟设备的本地 Web UI 支持的浏览器的列表。

|浏览器  |版本  |其他要求/说明  |
|---------|---------|---------|
|Google Chrome   |最新版本         |         |
|Microsoft Edge    | 最新版本        |         |
|Internet Explorer     | 最新版本        |         |
|FireFox    |最新版本         |         |


## <a name="networking-requirements"></a>网络要求

下表列出了需要在防火墙中打开以允许 SMB、云或管理流量的端口。 在此表中，*入*或*入站*表示传入客户端请求访问设备的方向。 *出*或*出站*表示 Data Box Gateway 设备从外部（超出部署范围）发送数据的方向：例如，到 Internet 的出站。

| 端口号。| 入或出 | 端口范围| 必选|   说明                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|出|WAN |否|出站端口用于 Internet 访问以检索更新。 <br>出站 Web 代理可由用户配置。 |                          
| TCP 443 (HTTPS)|出|WAN|是|出站端口用于访问云中的数据。<br>用户可配置出站 Web 代理。|   
| UDP 53 (DNS)|出|WAN|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 DNS 服务器时，才需要此端口。<br>我们建议使用本地 DNS 服务器。 |
| UDP 123 (NTP)|出|WAN|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 NTP 服务器时，才需要此端口。  |
| UDP 67 (DHCP)|出|WAN|某些情况下<br>请参阅说明|仅当使用 DHCP 服务器时，才需要此端口。  |
| TCP 80 (HTTP)|In|LAN|是|这是设备上用于本地管理的本地 UI 的入站端口。 <br>通过 HTTP 访问本地 UI 会自动重定向到 HTTPS。  | 
| TCP 443 (HTTPS)|In|LAN|是|这是设备上用于本地管理的本地 UI 的入站端口。 | 

## <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式

通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 Data Box Gateway 设备和 Data Box Gateway 服务依赖于其他 Microsoft 应用程序，例如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 反之，这会要求网络管理员在需要时为 Data Box Gateway 监视和更新防火墙规则。

绝大多数情况下，建议基于 Data Box Gateway 固定 IP 地址为出站流量设置防火墙规则。 但是，下面的信息可用于设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> - 设备（源）IP 应始终设置为所有已启用云的网络接口。
> - 目标 IP 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

|     URL 模式                                                                                                                                                                                                                                                                                                                                                                                                                                       |     组件/功能                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Azure Data Box Gateway 服务<br>Azure 服务总线<br>身份验证服务    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    设备激活                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    证书吊销                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Azure 存储帐户和监视                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Microsoft 更新服务器                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    支持包                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Windows 中的遥测服务，请参阅客户体验和诊断遥测的更新      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Internet 带宽

以下要求适用于可供 Data Box Gateway 服务使用的最低 Internet 带宽。

- Data Box Gateway 始终有专用的 20 Mbps Internet 带宽（或更高带宽）。 此带宽不应与任何其他应用程序共享。 
- 当使用网络限制时，Your Data Box Gateway 有专用的 32 Mbps Internet 带宽（或更高带宽）。

## <a name="next-step"></a>后续步骤

* [部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)


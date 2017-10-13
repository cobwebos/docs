---
title: "Microsoft Azure StorSimple 虚拟阵列系统要求 | Microsoft Docs"
description: "了解有关 StorSimple Virtual Array 的软件和网络要求的详细信息"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/10/2017
ms.author: alkohli
ms.openlocfilehash: 178bdfd7bf2eb162e7a94f3eef9c44c5ff646fb9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-system-requirements"></a>StorSimple 虚拟阵列系统要求
## <a name="overview"></a>概述
本文介绍了 Microsoft Azure StorSimple 虚拟阵列和访问阵列的存储客户端的重要系统要求。 建议在部署 StorSimple 系统之前仔细查看信息，并且在进行部署和后续操作过程中按需重新参阅。

系统要求包括：

* **存储客户端的软件要求** - 介绍受支持的虚拟化平台、Web 浏览器、iSCSI 发起程序、SMB 客户端、最低虚拟设备要求，以及这些操作系统的任何其他要求。
* **StorSimple 设备的网络要求** - 提供有关需要在防火墙中打开以允许 iSCSI、云和管理流量的端口的信息。

本文中发布的 StorSimple 系统要求信息仅适用于 StorSimple Virtual Arrays。

* 对于 8000 系列设备，请转到 [System requirements for your StorSimple 8000 series device](storsimple-system-requirements.md)（StorSimple 8000 系列设备的系统要求）。
* 对于 7000 系列设备，请转到 [System requirements for your StorSimple 5000-7000 series device](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)（StorSimple 5000-7000 系列设备的系统要求）。

## <a name="software-requirements"></a>软件要求
软件要求包括有关受支持的 Web 浏览器、SMB 版本、虚拟化平台和最低虚拟设备要求的信息。

### <a name="supported-virtualization-platforms"></a>受支持的虚拟化平台
| **虚拟机监控程序** | **版本** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 和更高版本 |
| VMware ESXi |5.5 和 6.0 |

### <a name="virtual-device-requirements"></a>虚拟设备要求
| **组件** | **要求** |
| --- | --- |
| 虚拟处理器的最小数目（核） |4 |
| 最小内存 (RAM) |8 GB <br> 对于文件服务器，小于 200 万个文件时为 8 GB，200 万 - 400 万个文件时为 16 GB|
| 磁盘空间<sup>1</sup> |OS 磁盘 - 80 GB <br></br>数据磁盘 - 500 GB 到 8 TB |
| 网络接口的最小数目 |1 |
| 最小 Internet 带宽<sup>2</sup> |5 Mbps |

<sup>1</sup> - 精简预配

<sup>2</sup> - 网络要求可能会根据每日数据更改频率而有所不同。 例如，如果设备在一天中需要备份 10 GB 或更多更改，则通过 5 Mbps 连接进行的每日备份可能耗时长达 4.25 小时（如果无法压缩数据或删除重复数据）。

### <a name="supported-web-browsers"></a>受支持的 Web 浏览器
| **组件** | **版本** | **其他要求/说明** |
| --- | --- | --- |
| Microsoft Edge |最新版本 | |
| Internet Explorer |最新版本 |使用 Internet Explorer 11 测试 |
| Google Chrome |最新版本 |使用 Chrome 46 测试 |

### <a name="supported-storage-clients"></a>受支持的存储客户端
以下软件要求适用于访问 StorSimple Virtual Array（配置为 iSCSI 服务器）的 iSCSI 发起程序。

| **受支持的操作系统** | **所需版本** | **其他要求/说明** |
| --- | --- | --- |
| Windows Server |2008R2 SP1、2012 和 2012R2 |StorSimple 可以创建既精简预配和完全预配的卷。 但无法创建部分预配的卷。 以下各项只支持 StorSimple iSCSI 卷： <ul><li>Windows 基本磁盘上的简单卷。</li><li>用于格式化卷的 Windows NTFS。</li> |

以下软件要求适用于访问 StorSimple Virtual Array（配置为文件服务器）的 SMB 客户端。

| **SMB 版本** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> 不要将受 Windows 加密文件系统 (EFS) 保护的文件复制或存储到 StorSimple 虚拟阵列文件服务器；这会导致不受支持的配置。 
> 

### <a name="supported-storage-format"></a>受支持的存储格式
仅支持 Azure 块 blob 存储。 页 blob 不受支持。 请查阅[有关块 blob 和页 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 的更多信息。

## <a name="networking-requirements"></a>网络要求
下表列出了需要在防火墙中打开以允许 iSCSI、SMB、云或管理流量的端口。 在此表中，*入*或*入站*指传入客户端请求访问设备的方向。 *出*或*出站*指 StorSimple 设备越过部署向外部发送数据的方向：例如，到 Internet 的出站。

| **端口号<sup>1</sup>** | **入或出** | **端口范围** | **必需** | **说明** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |出 |WAN |否 |出站端口用于 Internet 访问以检索更新。 <br></br>用户可配置出站 Web 代理。 |
| TCP 443 (HTTPS) |出 |WAN |是 |出站端口用于访问云中的数据。 <br></br>用户可配置出站 Web 代理。 |
| UDP 53 (DNS) |出 |WAN |在某些情况下；请参阅说明。 |仅当使用基于 Internet 的 DNS 服务器时，才需要此端口。 <br></br> 注意，如果部署文件服务器，建议使用本地 DNS 服务器。 |
| UDP 123 (NTP) |出 |WAN |在某些情况下；请参阅说明。 |仅当使用基于 Internet 的 NTP 服务器时，才需要此端口。<br></br> 注意，如果部署文件服务器，建议与 Active Directory 域控制器同步时间。 |
| TCP 80 (HTTP) |In |LAN |是 |这是 StorSimple 设备上用于本地管理的本地 UI 的入站端口。 <br></br> 注意，通过 HTTP 访问本地 UI 会自动重定向到 HTTPS。 |
| TCP 443 (HTTPS) |In |LAN |是 |这是 StorSimple 设备上用于本地管理的本地 UI 的入站端口。 |
| TCP 3260 (iSCSI) |In |LAN |否 |此端口用于通过 iSCSI 访问数据。 |

<sup>1</sup> 无需在公共 Internet 上打开任何入站端口。

> [!IMPORTANT]
> 请确保防火墙不会修改或解密 StorSimple 设备和 Azure 之间的任何 SSL 流量。
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式
通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 虚拟阵列和 StorSimple Device Manager 服务依赖于其他的 Microsoft 应用程序，如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 反之，这会要求网络管理员在需要时为 StorSimple 监视和更新防火墙规则。 

绝大多数情况下，建议基于 StorSimple 固定 IP 地址为出站流量设置防火墙规则。 但是，也可以使用以下信息设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> 
> * 设备（源）IP 应始终设置为所有已启用云的网络接口。 
> * 目标 IP 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)。
> 
> 

| URL 模式 | 组件/功能 |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` |StorSimple Device Manager 服务<br>访问控制服务<br>Azure 服务总线 |
| `http://*.backup.windowsazure.com` |设备注册 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |证书吊销 |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 存储帐户和监视 |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft 更新服务器<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |支持包 |
| `http://*.data.microsoft.com ` |Windows 中的遥测服务，请参阅[update for customer experience and diagnostic telemetry](https://support.microsoft.com/en-us/kb/3068708)（客户体验和诊断遥测的更新） |

## <a name="next-step"></a>后续步骤
* [Prepare the portal to deploy your StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)（准备门户以部署 StorSimple Virtual Array）


---
title: "StorSimple 系统要求 | Microsoft Docs"
description: "介绍 Microsoft Azure StorSimple 解决方案的软件、网络和高可用性要求以及最佳实践。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2b6ca34a-d758-48e7-ab1e-4fdd80cf48d4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 55e1cd90df0e4413ce027361c636257d823a50d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-software-high-availability-and-networking-requirements"></a>StorSimple 软件、高可用性和网络要求
## <a name="overview"></a>概述
欢迎使用 Microsoft Azure StorSimple。 本文介绍了 StorSimple 设备以及访问此设备的存储客户端的重要系统要求和最佳实践。 建议在部署 StorSimple 系统之前仔细查看信息，并且在进行部署和后续操作过程中按需重新参阅。

系统需求包括：

* **存储客户端的软件要求**介绍支持的操作系统以及针对这些操作系统的任何其他要求。
* **StorSimple 设备的网络要求** 提供有关需要在防火墙中打开的端口的信息，以便允许传输 iSCSI、云或管理流量。
* **StorSimple 的高可用性要求** - 介绍 StorSimple 设备和主机计算机的高可用性要求和最佳实践。 

## <a name="software-requirements-for-storage-clients"></a>存储客户端的软件要求
以下软件要求适用于访问 StorSimple 设备的存储客户端。

| 支持的操作系统 | 所需版本 | 其他要求/说明 |
| --- | --- | --- |
| Windows Server |2008R2 SP1、2012、2012R2、2016 |仅支持将 StorSimple iSCSI 卷用于以下 Windows 磁盘类型：<ul><li>基本磁盘上的简单卷</li><li>动态磁盘上的简单和镜像卷</li></ul>仅支持操作系统中原本就存在的软件 iSCSI 发起程序。 不支持硬件 iSCSI 发起程序。<br></br>如果使用 StorSimple iSCSI 卷，则支持 Windows Server 2012 和 2016 的精简预配和 ODX 功能。<br><br>StorSimple 可以创建既精简预配和完全预配的卷。 但无法创建部分预配的卷。<br><br>重新格式化精简预配的卷可能需要很长时间。 建议删除该卷，并新建一个卷而不是重新格式化。 但是，如果仍希望重新格式化卷：<ul><li>请在重新格式化之前运行以下命令，以免造成空间回收延迟： <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>格式化完成后，请使用以下命令重新启用空间回收功能：<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>如 [KB 2878635](https://support.microsoft.com/kb/2870270) 中所述，对 Windows Server 计算机应用 Windows Server 2012 修补程序。</li></ul></li></ul></ul> 如果要配置 StorSimple Snapshot Manager 或 StorSimple Adapter for SharePoint，请转到[可选组件的软件要求](#software-requirements-for-optional-components)。 |
| VMWare ESX |5.5 和 6.0 |支持将 VMWare vSphere 作为 iSCSI 客户端。 StorSimple 设备上的 VMware vSphere 支持 VAAI 块功能。 |
| Linux RHEL/CentOS |5、6 和 7 |支持配有开放 iSCSI 发起程序版本 5、6 和 7 的 Linux iSCSI 客户端。 |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> StorSimple 当前不支持 IBM AIX。
> 
> 

## <a name="software-requirements-for-optional-components"></a>可选组件的软件要求
以下软件要求适用于可选的 StorSimple 组件（StorSimple Snapshot Manager 和 StorSimple Adapter for SharePoint）。

| 组件 | 主机平台 | 其他要求/说明 |
| --- | --- | --- |
| StorSimple 快照管理器 |Windows Server 2008R2 SP1、2012、2012R2 |执行镜像动态磁盘的备份/还原以及任何应用程序的一致性备份要求在 Windows Server 上使用 StorSimple Snapshot Manager。<br> 仅 Windows Server 2008 R2 SP1（64 位）、Windows 2012 R2 和 Windows Server 2012 支持 StorSimple Snapshot Manager。<ul><li>如果使用的是 Window Server 2012，则必须在安装 StorSimple Snapshot Manager 之前安装 .NET 3.5–4.5。</li><li>如果使用的是 Windows Server 2008 R2 SP1，则必须在安装 StorSimple Snapshot Manager 之前安装 Windows Management Framework 3.0。</li></ul> |
| StorSimple Adapter for SharePoint |Windows Server 2008R2 SP1、2012、2012R2 |<ul><li>仅 SharePoint 2010 和 SharePoint 2013 支持 StorSimple Adapter for SharePoint。</li><li>RBS 要求使用 SQL Server Enterprise Edition、版本 2008 R2 或 2012。</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>StorSimple 设备的网络要求
StorSimple 设备是锁定设备。 但是，需要在防火墙中打开端口以允许传输 iSCSI、云和管理流量。 下表列出了需要在防火墙中打开的端口。 在此表中，*入*或*入站*表示传入客户端请求访问设备的方向。 *出*或*出站*表示 StorSimple 设备从外部（超出部署范围）发送数据的方向：例如，到 Internet 的出站。

| 端口号 <sup>1、2</sup> | 入或出 | 端口范围 | 必选 | 说明 |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |出 |WAN |否 |<ul><li>出站端口用于 Internet 访问以检索更新。</li><li>出站 Web 代理可由用户配置。</li><li>若要允许系统更新，还必须为控制器的固定 IP 打开此端口。</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |出 |WAN |是 |<ul><li>出站端口用于访问云中的数据。</li><li>出站 Web 代理可由用户配置。</li><li>若要允许系统更新，还必须为控制器的固定 IP 打开此端口。</li><li>此端口还在两个控制器中用于垃圾回收。</li></ul> |
| UDP 53 (DNS) |出 |WAN |在某些情况下；请参阅说明。 |仅当使用基于 Internet 的 DNS 服务器时，才需要此端口。 |
| UDP 123 (NTP) |出 |WAN |在某些情况下；请参阅说明。 |仅当使用基于 Internet 的 NTP 服务器时，才需要此端口。 |
| TCP 9354 |出 |WAN |是 |StorSimple 设备使用出站端口与 StorSimple Manager 服务进行通信。 |
| 3260 (iSCSI) |In |LAN |否 |此端口用于通过 iSCSI 访问数据。 |
| 5985 |In |LAN |否 |StorSimple Snapshot Manager 使用入站端口与 StorSimple 设备进行通信。<br>通过 HTTP 远程连接到 Windows PowerShell for StorSimple 时，也会使用此端口。 |
| 5986 |In |LAN |否 |通过 HTTPS 远程连接到 Windows PowerShell for StorSimple 时使用此端口。 |

<sup>1</sup> 无需在公共 Internet 上打开任何入站端口。

<sup>2</sup> 如果多个端口采用一个网关配置，将基于如下[端口路由](#routing-metric)中所述的端口路由顺序来确定出站路由流量顺序。

<sup>3</sup> StorSimple 设备上控制器的固定 IP 必须可路由，并能直接连接到 Internet 或通过配置 Web 代理连接到 Internet。 固定 IP 地址用于为设备提供更新。 如果设备控制器无法通过固定 IP 连接到 Internet，用户将不能更新 StorSimple 设备。

> [!IMPORTANT]
> 确保防火墙不会修改或解密 StorSimple 设备和 Azure 之间的任何 SSL 通信。
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式
通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 StorSimple 设备和 StorSimple Manager 服务取决于其他 Microsoft 应用程序，如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 反之，这会要求网络管理员在需要时为 StorSimple 监视和更新防火墙规则。

绝大多数情况下，建议基于 StorSimple 固定 IP 地址为出站流量设置防火墙规则。 但是，下面的信息可用于设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> 设备（源）IP 应始终设置为所有已启用的网络接口。 目标 IP 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)。
> 
> 

#### <a name="url-patterns-for-azure-portal"></a>Azure 门户的 URL 模式
| URL 模式 | 组件/功能 | 设备 IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple Device Manager 服务<br>访问控制服务<br>Azure 服务总线<br>身份验证服务 |启用云的网络接口 |
| `https://*.backup.windowsazure.com` |设备注册 |仅限 DATA 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |证书吊销 |启用云的网络接口 |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 存储帐户和监视 |启用云的网络接口 |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft 更新服务器<br> |仅限控制器的固定 IP |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |仅限控制器的固定 IP |
| `https://*.partners.extranet.microsoft.com/*` |支持包 |启用云的网络接口 |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure Government 门户的 URL 模式
| URL 模式 | 组件/功能 | 设备 IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*` <br>`https://login-us.microsoftonline.com` |StorSimple Device Manager 服务<br>访问控制服务<br>Azure 服务总线<br>身份验证服务 |启用云的网络接口 |
| `https://*.backup.windowsazure.us` |设备注册 |仅限 DATA 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |证书吊销 |启用云的网络接口 |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 存储帐户和监视 |启用云的网络接口 |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft 更新服务器<br> |仅限控制器的固定 IP |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |仅限控制器的固定 IP |
| `https://*.partners.extranet.microsoft.com/*` |支持包 |启用云的网络接口 |

### <a name="routing-metric"></a>路由跃点数
路由跃点数与将数据路由到指定网络的接口和网关相关联。 如果路由协议了解到存在到达同一目标的多个路径，它会使用路由跃点数来计算给定目标的最佳路径。 路由跃点数越低，优先级越高。

在 StorSimple 的上下文中，如果配置多个网络接口和网关以传送流量，路由跃点数将发挥作用，帮助确定这些接口的相对使用顺序。 用户不能更改路由跃点数。 但是，可以使用 `Get-HcsRoutingTable` cmdlet 在 StorSimple 设备上打印出路由表（和跃点数）。 有关 Get-HcsRoutingTable cmdlet 的详细信息，请参阅[排查 StorSimple 设备部署问题](storsimple-troubleshoot-deployment.md)。

路由跃点数算法因 StorSimple 设备上运行的软件版本而异。

**Update 1 之前的版本**

这包括 Update 1 之前的软件版本，例如 GA、0.1、0.2 或 0.3 版本。 基于路由跃点数的顺序如下所示：

   *上次配置的 10 GbE 网络接口 > 其他 10 GbE 网络接口 > 上次配置的 1 GbE 网络接口 > 其他 1 GbE 网络接口*

**Update 1 之后、Update 2 之前的版本**

这包括软件版本 1、1.1、1.2 等。 按如下规则确定基于路由跃点数的顺序：

   *DATA 0 > 上次配置的 10 GbE 网络接口 > 其他 10 GbE 网络接口 > 上次配置的 1 GbE 网络接口 > 其他 1 GbE 网络接口*

   在 Update 1 中，DATA 0 的路由跃点数最低；因此，所有云流量都通过 DATA 0 路由。 如果 StorSimple 设备上有多个启用云的网络接口，请将这点记录下来。

**从 Update 2 开始的版本**

Update 2 包含几项与网络有关的改进，路由跃点数也有变化。 该行为如下所述。

* 已分配给网络接口一组预先确定的值。     
* 请考虑如下所示的示例表，其中列出了在各种网络接口已启用云或禁用云但具有配置网关时，向它们分配的值。 请注意此处分配的值只是示例值。

    | 网络接口 | 启用云 | 禁用云但配有网关 |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | #N/A            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* 通过网络接口路由云流量的顺序是：
  
    *Data 0 > Data 1 > Date 2 > Data 3 > Data 4 > Data 5*
  
    可以通过以下示例解释这个顺序。
  
    请考虑具有两个启用云的网络接口 Data 0 和 Data 5 的 StorSimple 设备。 Data 1 到 Data 4 是禁用云但具有配置网关的接口。 该设备的流量路由顺序是：
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *其中，括号中的数字表示相应的路由跃点数。*
  
    如果 Data 0 失败，将通过 Data 5 路由云流量。 假设所有其他网络上已配置网关，如果 Data 0 和 Data 5 均失败，将通过 Data 1 路由云流量。
* 如果启用云的网络接口失败，则重试连接到接口 3 次，其中有 30 秒的延迟。 如果所有重试均失败，将流量路由到路由表确定的下一个启用云的可用接口。 如果所有启用云的网络接口均失败，设备将故障转移到另一个控制器（在这种情况下不用重新启动）。
* 如果启用 iSCSI 的网络接口发生 VIP 失败，将执行 3 次重试，其中有 2 秒的延迟。 此行为与此前的版本相同。 如果所有的 iSCSI 网络接口均失败，会发生控制器故障转移（伴随有重新启动）。
* VIP 失败还会在 StorSimple 设备上引发警报。 有关详细信息，请转到[警报快速参考](storsimple-manage-alerts.md)。
* 对于重试，iSCSI 优先于云。
  
    请考虑下面的示例：StorSimple 设备已启用两个网络接口，即 Data 0 和 Data 1。 Data 0 是启用云的接口，而 Data 1 既启用云又启用了 iSCSI。 此设备上的其他网络接口均未启用云或 iSCSI。
  
    如果 Data 1 失败，因为它是最后一个 iSCSI 网络接口，会导致将控制器故障转移到另一个控制器上的 Data 1。

### <a name="networking-best-practices"></a>网络最佳做法
除上述网络要求外，为了获得 StorSimple 解决方案的最佳性能，请遵循以下最佳实践：

* 确保 StorSimple 设备具有始终可用的、专用的 40 Mbps 带宽（或更高带宽）。 此带宽不应与任何其他应用程序共享（或应确保通过使用 QoS 策略进行分配）。
* 确保到 Internet 的网络连接始终可用。 到设备的偶发性或不可靠 Internet 连接（包括无 Internet 连接）将导致不支持的配置。
* 在设备上使用专用的网络接口来访问 iSCSI 和云，从而隔离 iSCSI 和云流量。 有关详细信息，请参阅如何在 StorSimple 设备上[修改网络接口](storsimple-modify-device-config.md#modify-network-interfaces)。
* 请勿对网络接口使用链接聚合控制协议 (LACP) 配置。 这是不受支持的配置。

## <a name="high-availability-requirements-for-storsimple"></a>StorSimple 的高可用性要求
StorSimple 解决方案包含的硬件平台提供了可用性和可靠性功能，可为数据中心内高度可用的容错存储器基础结构提供基础。 但是，用户应遵守相应的要求和最佳实践，以帮助确保 StorSimple 解决方案的可用性。 部署 StorSimple 之前，请仔细查看 StorSimple 设备和连接的主机计算机的以下要求和最佳实践。

有关监控和维护 StorSimple 设备的硬件组件的详细信息，请转到[使用 StorSimple Manager 服务监控硬件组件和状态](storsimple-monitor-hardware-status.md)以及 [StorSimple 硬件组件更换](storsimple-hardware-component-replacement.md)。

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>StorSimple 设备的高可用性要求和过程
请仔细查看以下信息，确保 StorSimple 设备具有高可用性。

#### <a name="pcms"></a>PCM
StorSimple 设备包括冗余的热插拔电源和散热模块 (PCM)。 每个 PCM 都有足够的容量，能够为整个机箱提供服务。 若要确保高可用性，必须安装两个 PCM。

* 请将 PCM 连接到不同电源，以便在电源出现故障时提供可用性。
* 如果 PCM 发生故障，立即请求更换。
* 仅在有更换品并准备好安装时，才能移除发生故障的 PCM。
* 不要同时移除两个 PCM。 PCM 模块包括备用电池模块。 同时移除两个 PCM 将导致在没有电池保护的情况下关机，设备状态将无法保存。 有关电池的详细信息，请转到[维护备用电池模块](storsimple-battery-replacement.md#maintain-the-backup-battery-module)。

#### <a name="controller-modules"></a>控制器模块
StorSimple 设备包括冗余的热插拔控制器模块。 控制器模块以主动/被动方式运行。 在任何给定的时间，一个控制器模块处于主动状态并且正在提供服务，而另一个控制器模块处于被动状态。 如果主动控制器模块发生故障或被移除，被动控制器模块会通电并开始运行。 每个控制器模块都有足够的容量，能够为整个机箱提供服务。 必须安装两个控制器模块以确保高可用性。

* 请确保始终都安装有两个控制器模块。
* 如果控制器模块发生故障，立即请求更换。
* 仅在有更换品并准备好安装时，才能移除发生故障的控制器模块。 持续时间过长的模块移除过程会影响气流，进而影响系统冷却。
* 请确保到两个控制器模块的网络连接相同并且连接的网络接口具有相同的网络配置。
* 如果控制器模块发生故障或需要更换，在更换发生故障的控制器模块之前，请确保另一个控制器模块处于主动状态。 若要验证控制器是否处于主动状态，请转到 [Identify the active controller on your device](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)（识别设备上的主动控制器）。
* 不要同时移除两个控制器模块。 如果正在进行控制器故障转移，请不要关闭备用控制器模块或从机箱中移除它。
* 控制器故障转移完成后，请等待至少五分钟，再移除其中任意一个控制器模块。

#### <a name="network-interfaces"></a>网络接口
每个 StorSimple 设备控制器模块都有四个 1 千兆位和两个 10 千兆位以太网网络接口。

* 请确保到两个控制器模块的网络连接相同，并且控制器模块接口连接的网络接口具有相同的网络配置。
* 如果可能，请在不同的交换机中部署网络连接，确保在发生网络设备故障时的服务可用性。
* 拔出唯一或剩余的最后一个启用 iSCSI 的接口（已分配 IP）时，先禁用此接口，再拔出电缆。 如果先拔出此接口，将导致主动控制器故障转移到被动控制器。 如果与被动控制器对应的接口也被拔出，则两个控制器会在选定一个控制器之前多次重新启动。
* 从每个控制器模块将至少两个 DATA 接口连接到网络。
* 如果已启用两个 10 GbE 接口，请将它们部署在不同的交换机上。
* 如果可能，请在服务器上使用 MPIO，确保服务器可以容许链接、网络或接口故障。

有关将设备联网以实现高可用性和性能的详细信息，请转到[安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)或[安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)。

#### <a name="ssds-and-hdds"></a>SSD 和 HDD
StorSimple 设备包括使用镜像空间进行保护的固态硬盘 (SSD) 和硬盘驱动器 (HDD)。 使用镜像空间可确保设备容许一个或多个 SSD 或 HDD 发生故障。

* 请确保所有的 SSD 和 HDD 模块均已安装。
* 如果 SSD 或 HDD 发生故障，立即请求更换。
* 如果 SSD 或 HDD 发生故障或需要更换，请确保仅移除需要更换的 SSD 或 HDD。
* 不要同时从系统中移除多个 SSD 或 HDD。
  如果短时间内 2 个或更多个特定类型（SSD、HDD）的磁盘发生故障或发生连续故障，可能导致系统出现故障和潜在的数据丢失。 如果发生这种情况，请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)获取帮助。
* 更换过程中，请在 SSD 和 HDD 驱动器的“**维护**”页面中监视“**硬件状态**”。 绿色复选标记状态表示磁盘运行正常，而红色感叹号表示 SSD 或 HDD 发生故障。
* 建议用户为需要系统故障保护的所有卷配置云快照。

#### <a name="ebod-enclosure"></a>EBOD 机箱
除了主机箱，8600 型号的 StorSimple 设备还包括扩展磁盘组 (EBOD) 机箱。 EBOD 包括使用镜像空间进行保护的 EBOD 控制器和硬盘驱动器 (HDD)。 使用镜像空间可确保设备能够容许一个或多个 HDD 发生故障。 EBOD 机箱通过冗余 SAS 电缆连接到主机箱。

* 请确保始终都安装有两个 EBOD 机箱控制器模块、两个 SAS 电缆以及所有的硬盘驱动器。
* 如果 EBOD 机箱控制器模块发生故障，立即请求更换。
* 如果 EBOD 机箱控制器模块发生故障，请确保在更换发生故障的模块之前另一个控制器模块处于主动状态。 若要验证控制器是否处于主动状态，请转到 [Identify the active controller on your device](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)（识别设备上的主动控制器）。
* 在 EBOD 控制器模块更换过程中，通过访问“**维护**” > “**硬件状态**”持续监视 StorSimple Manager 服务中的组件状态。
* 如果 SAS 电缆发生故障或需要更换（应让 Microsoft 支持部门参与，一起做出决定），请确保仅移除需要更换的 SAS 电缆。
* 不要同时从系统中移除两个 SAS 电缆。

### <a name="high-availability-recommendations-for-your-host-computers"></a>针对主机计算机的高可用性建议
请仔细查看这些最佳实践，确保连接到 StorSimple 设备的主机具有高可用性。

* 通过[双节点文件服务器群集配置][1]来配置 StorSimple。 通过消除单点故障并在主机端内置冗余，整个解决方案变得高度可用。
* 在存储控制器故障转移期间，使用可用于 Windows Server 2012 (SMB 3.0) 的连续可用 (CA) 共享来实现高可用性。 有关通过 Windows Server 2012 配置文件服务器群集和持续可用共享的其他信息，请参阅该[视频演示](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)。

## <a name="next-steps"></a>后续步骤
* [了解 StorSimple 系统限制](storsimple-limits.md)。
* [了解如何部署 StorSimple 解决方案](storsimple-deployment-walkthrough-u2.md)。

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

---
title: "部署本地 StorSimple 设备 | Microsoft Docs"
description: "介绍部署 StorSimple 设备和服务的步骤和最佳实践。 （适用于 Microsoft Azure StorSimple 0.3 和更低版本。）"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b27f87a2-1363-4e0d-90f7-37b5dd1f21c9
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 2063acbafd6766d00dee9509ee7def73bdc5b982
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-your-on-premises-storsimple-device"></a>部署本地 StorSimple 设备
> [!div class="op_single_selector"]
> * [Update 2 ](storsimple-deployment-walkthrough-u2.md)
> * [Update 1](storsimple-deployment-walkthrough-u1.md)
> * [正式版](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>概述
欢迎使用 Microsoft Azure StorSimple 设备部署。 这些部署教程适用于 StorSimple 8000 系列发行版、StorSimple 8000 系列 Update 0.1、StorSimple 8000 系列 Update 0.2 以及 StorSimple 8000 系列 Update 0.3。 本系列教程说明如何配置 StorSimple 设备，并包含配置清单、配置先决条件和详细配置步骤。

这些教程中的信息假设读者已阅读安全预防措施，并已打开 StorSimple 设备包、安装机架，并连接好电缆。 如果仍然需要执行这些任务，请先阅读 [安全预防措施](storsimple-safety.md)。 根据具体的设备型号，可以遵循以下说明打开包装、安装机架和连接电缆：

* [解包、装载机架，并将电缆接到 8100](storsimple-8100-hardware-installation.md)
* [解包、安装机架，将电缆连接到 8600](storsimple-8600-hardware-installation.md)

需要有管理员权限才能完成安装和配置过程。 建议在开始之前查看配置清单。 部署和配置过程可能需要一些时间才能完成。

> [!NOTE]
> Microsoft Azure 网站上发布的 StorSimple 部署信息仅适用于 StorSimple 8000 系列设备。 有关 5000 和 7000 系列设备的完整信息，请转到： [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。 有关 5000 和 7000 系列的部署信息，请参阅 [StorSimple System Quick Start Guide](http://onlinehelp.storsimple.com/111_Appliance/)（StorSimple 系统快速入门指南）。
> 
> 

## <a name="deployment-steps"></a>部署步骤
执行这些所需的步骤来配置 StorSimple 设备，并将其连接到 StorSimple Manager 服务。 除了这些所需的步骤外，在部署过程中可能还需要完成一些可选步骤和过程。 逐步部署说明将指示何时应执行每个可选步骤。

| 步骤 | 说明 |
| --- | --- |
| **先决条件** |这些是针对即将进行的部署需要做好准备。 |
| 部署配置清单。 |使用此清单来收集和记录部署之前或期间的信息。 |
| 部署先决条件。 |这些项会验证环境是否已准备就绪以进行部署。 |
|  | |
| **逐步部署** |需要完成以下步骤在生产环境中部署 StorSimple 设备。 |
| 步骤 1：创建新服务。 |设置 StorSimple 设备的云管理和存储。 如果其他 StorSimple 设备有现有服务，请跳过此步骤。 |
| 步骤 2：获取服务注册密钥。 |使用此密钥来注册 StorSimple 设备，并将其连接到管理服务。 |
| 步骤 3：通过 Windows PowerShell for StorSimple 配置和注册设备。 |使用管理服务将设备连接到网络，并通过 Azure 注册以完成安装。 |
| 步骤 4：完成最低版本的设备安装</br>可选：更新 StorSimple 设备。 |使用管理服务完成设备设置，并使其提供存储。 |
| 步骤 5：创建卷容器。 |创建容器来预配卷。 卷容器具有其中所包含的所有卷的存储帐户、带宽和加密设置。 |
| 步骤 6：创建卷。 |在服务器的 StorSimple 设备上预配存储卷。 |
| 步骤 7：装载、初始化和格式化卷</br>可选：配置 MPIO。 |将服务器连接到设备提供的 iSCSI 存储。 根据情况配置 MPIO，以确保服务器可以容许链接、网络和接口故障。 |
| 步骤 8：执行备份。 |设置备份策略以保护数据 |
|  | |
| **其他过程** |在部署解决方案时可能需要参阅这些过程。 |
| 针对服务配置新的存储帐户。 | |
| 使用 PuTTY 连接到设备串行控制台。 | |
| 获取 Windows Server 主机的 IQN。 | |
| 创建手动备份。 | |

## <a name="deployment-configuration-checklist"></a>部署配置清单
以下部署配置清单描述在配置 StorSimple 设备上的软件之前需要收集的信息。 提前准备一些此类信息有助于简化环境中 StorSimple 设备的部署过程。 使用此清单记下部署设备时的配置详细信息。

| 阶段 | 参数 | 详细信息 | 值 |
| --- | --- | --- | --- |
| **连接设备电缆** |串行访问 |初始设备配置 |是/否 |
|  | | | |
| **配置和注册设备** |Data 0 网络设置 |Data 0 IP 地址：</br>子网掩码：</br>网关：</br>主 DNS 服务器：</br>主 NTP 服务器：</br>Web 代理服务器 IP/FQDN （可选）：</br>Web 代理端口： | |
| &nbsp; |设备管理员密码 |密码必须介于 8 和 15 个字符之间，包含小写字母、大写字母、数字和特殊字符。 | |
| &nbsp; |StorSimple 快照管理器密码 |密码必须是 14 或 15 个字符，包含小写字母、大写字母、数字和特殊字符。 | |
| &nbsp; |服务注册密钥 |此密钥从 Azure 经典门户生成。 | |
| &nbsp; |服务数据加密密钥 |当设备通过 Windows PowerShell for StorSimple 注册管理服务时会创建此密钥。 复制此密钥并将其保存到一个安全位置。 | |
|  | | | |
| **完成最低版本的设备安装** |设备的友好名称 |这是设备的描述性名称。 | |
| &nbsp; |时区 |设备将此时区用于所有计划操作。 | |
| &nbsp; |备用 DNS 服务器 |这是必需的配置。 | |
| &nbsp; |网络接口：Data 0 控制器固定 IP |这些 IP 应可路由到 Internet。</br>控制器 0 固定 IP 地址：</br>控制器 1 固定 IP 地址： | |
|  | | | |
| **其他网络接口设置** |网络接口：Data 1</br>如果已启用 iSCSI，请不要配置网关。 |用途：云/iSCSI/未使用</br>IP 地址：</br>子网掩码：</br>网关： | |
| &nbsp; |网络接口：Data 2</br>如果已启用 iSCSI，请不要配置网关。 |用途：云/iSCSI/未使用</br>IP 地址：</br>子网掩码：</br>网关： | |
| &nbsp; |网络接口：Data 3</br>如果已启用 iSCSI，请不要配置网关。 |用途：云/iSCSI/未使用</br>IP 地址：</br>子网掩码：</br>网关： | |
| &nbsp; |网络接口：Data 4</br>如果已启用 iSCSI，请不要配置网关。 |用途：云/iSCSI/未使用</br>IP 地址：</br>子网掩码：</br>网关： | |
| &nbsp; |网络接口：Data 5</br>如果已启用 iSCSI，请不要配置网关。 |用途：云/iSCSI/未使用</br>IP 地址：</br>子网掩码：</br>网关： | |
|  | | | |
| **创建卷容器** |卷容器名称： |容器的名称 | |
| &nbsp; |Azure 存储帐户： |与此卷容器关联的存储帐户名和访问密钥 | |
| &nbsp; |云存储加密密钥： |每个容器中的存储的加密密钥 | |
|  | | | |
| **创建卷** |每个卷的详细信息 |卷名： | |
| &nbsp; |&nbsp; |大小： | |
| &nbsp; |&nbsp; |使用类型： | |
| &nbsp; |&nbsp; |ACR 名称： | |
| &nbsp; |&nbsp; |默认备份策略： | |
|  | | | |
| **装载、初始化和格式化卷** |连接到存储的每个主机服务器的详细信息 |Windows Server 名称： | |
| &nbsp; |&nbsp; |Windows Server IQN： | |
| &nbsp; |&nbsp; |Windows Server 卷名： | |
| &nbsp; |&nbsp; |NTFS 装入点/驱动器号： | |

## <a name="deployment-prerequisites"></a>部署先决条件
以下部分说明了 StorSimple Manager 服务、StorSimple 设备和数据中心内网络的配置先决条件。

### <a name="for-the-storsimple-manager-service"></a>对于 StorSimple Manager 服务
在开始之前，请确保：

* 具有 Microsoft 帐户和访问凭据。
* 具有 Microsoft Azure 存储帐户和访问凭据。
* StorSimple Manager 服务已启用 Microsoft Azure 订阅。 应通过 [企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)购买订阅。
* 有权访问终端模拟软件，如 PuTTY。

### <a name="for-the-device-in-the-datacenter"></a>对于数据中心中的设备
配置设备之前，请确保：

* 已完全解包、装载在机架上，并已连接所有电源、网络和串行访问线缆，如下所述：
  
  * [解包、装载机架，并将电缆接到 8100 设备](storsimple-8100-hardware-installation.md)
  * [解包、装载机架，并电缆接到 8600 设备](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络
在开始之前，请确保：

* 数据中心防火墙的端口已开放，以允许 iSCSI 和云流量，如 [StorSimple 设备的网络要求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)中所述。
* 数据中心内的设备可以连接到外部网络。 运行以下 [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) cmdlet（如下表中所示），验证与外部网络的连接。 请在已连接到 Azure、要在其中部署 StorSimple 设备的计算机上（在数据中心网络内）执行此项验证。  

| 对于此参数... | 检查有效性... | 运行这些命令/cmdlet。 |
| --- | --- | --- |
| **IP**</br>**子网**</br>**网关** |这是有效的 IPv4 或 IPv6 地址吗？</br>这是有效的子网吗？</br>这是有效的网关吗？</br>这是网络上的重复 IP 吗？ |`ping ip`</br>`arp -a`</br>`ping` 和 `arp` 命令应会失败，指出数据中心网络中没有任何设备正在使用此 IP。 |
|  | | |
| **DNS** |这是有效的 DNS 且可以解析 Azure URL 吗？ |`Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>可以使用的替代命令是：</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com` |
| &nbsp; |检查端口 53 是否打开。 仅当为设备使用外部 DNS 时才适用。 内部 DNS 应自动解析外部 URL。 |`Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[有关此 cmdlet 的详细信息](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/) |
|  | | |
| **NTP** |输入 NTP 服务器时立即触发时间同步。 输入 `time.windows.com` 或公共时间服务器时检查 UDP 端口 123 是否打开。 |[下载并使用此脚本](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca)。 |
|  | | |
| **代理（可选）** |这是有效的代理 URI 和端口吗？ </br> 身份验证模式正确吗？ |<code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>此命令应在配置 Web 代理后立即运行。 如果返回状态代码 200，表示连接成功。 |
| &nbsp; |流量可通过代理路由吗？ |在设备上配置代理后，运行 DNS 验证、NTP 检查或 HTTP 检查。 这可以明确反映流量是否在代理上或其他位置遭到阻止。 |
|  | | |
| **注册** |检查出站 TCP 端口 443、80、9354 是否打开。 |`Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[有关 Test-NetConnection cmdlet 的详细信息](https://technet.microsoft.com/library/dn372891.aspx) |

## <a name="step-by-step-deployment"></a>逐步部署
使用下面的分步说明在数据中心部署 StorSimple 设备。

## <a name="step-1-create-a-new-service"></a>步骤 1：创建新服务
StorSimple Manager 服务可以管理多个 StorSimple 设备。 部署第一个 StorSimple 设备时，需要创建新的 StorSimple Manager 服务。

> [!IMPORTANT]
> 如果已有 StorSimple Manager 服务，并且想要使用该服务部署 StorSimple 设备，请跳过本步骤。
> 
> 

执行以下步骤创建新的 StorSimple Manager 服务实例。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> 如果未启用服务的自动创建存储帐户，则在成功创建服务后，需要创建至少一个存储帐户。 在创建卷容器时会使用此帐户。
> 
> 如果未自动创建存储帐户，请转到 [针对服务配置新的存储帐户](#configure-a-new-storage-account-for-the-service) 了解详细说明。
> 如果启用了自动创建存储帐户，请转到 [步骤 2：获取服务注册密钥](#step-2:-get-the-service-registration-key)。
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>步骤 2：获取服务注册密钥
在 StorSimple Manager 服务启动后和运行中时，将需要获取服务注册密钥。 此密钥可用来注册 StorSimple 设备，并将其连接到服务。

在 Azure 经典门户中执行以下步骤。

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>步骤 3：通过 Windows PowerShell for StorSimple 配置和注册设备
> [!IMPORTANT]
> 在执行此项配置之前，请拔下两个（主动和被动）控制器上除 DATA 0 以外的所有网络接口。
> 
> 

使用 Windows PowerShell for StorSimple 来完成 StorSimple 设备的初始设置，如以下过程所述。 需要使用终端模拟软件来完成此步骤。 有关详细信息，请参阅 [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console)。

[!INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>步骤 4：完成最低版本的设备安装
若要完成 StorSimple 设备的最起码设备配置，需要：

* 设置备用 DNS 服务器。
* 至少在一个网络接口上启用 iSCSI。
* 将固定的 IP 地址分配给两个控制器。

在 Azure 经典门户中执行以下步骤，完成最低版本的设备安装。

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

完成设备配置后，必须扫描更新（如果有）并安装更新。 更新可能需花费数小时才能完成。 请遵循 [扫描并应用更新](#scan-for-and-apply-updates)中的说明。

## <a name="step-5-create-a-volume-container"></a>步骤 5：创建卷容器
卷容器具有其中所包含的所有卷的存储帐户、带宽和加密设置。 需要创建卷容器后才可以开始在 StorSimple 设备上预配卷。

请在 Azure 经典门户中执行以下步骤，创建卷容器。

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>步骤 6：创建卷
创建卷容器后，就可以为服务器在 StorSimple 设备上设置存储卷。 请在 Azure 经典门户中执行以下步骤，创建卷。

> [!IMPORTANT]
> StorSimple Manager 只能创建既精简预配的卷。  无法创建完全预配或部分预配的卷。
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>步骤 7：装载、初始化和格式化卷
> [!IMPORTANT]
> * 为了获得 StorSimple 解决方案的高可用性，建议在 Windows Server 主机上配置 iSCSI 之前，先在 Windows Server 主机上配置 MPIO（可选）。 主机服务器上的 MPIO 配置将确保服务器可以容许链接、网络或接口故障。
> * 有关 MPIO 和 iSCSI 安装与配置说明，请转到 [为 StorSimple 设备配置 MPIO](storsimple-configure-mpio-windows-server.md)。 这些内容还将包括装载、初始化和格式化 StorSimple 卷的步骤。
> 
> 

如果决定不配置 MPIO，请执行以下步骤来装载、初始化和格式化 StorSimple 卷。

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>步骤 8：执行备份
备份可提供卷的时间点保护，并可提高可恢复性，同时最大限度地减少恢复时间。 可以在 StorSimple 设备上执行两种类型的备份：本地快照和云快照。 上述每种备份类型都可以是**计划**或**手动**的。

请在 Azure 经典门户中执行以下步骤，创建计划备份。

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

可以随时进行手动备份。 如需相关过程，请转到 [创建手动备份](#Create-a-manual-backup)。

## <a name="configure-a-new-storage-account-for-the-service"></a>针对服务配置新的存储帐户
这是一个可选步骤，只有当未启用服务自动创建存储帐户时，才需要执行。 必须要具有 Microsoft Azure 存储帐户才可以创建 StorSimple 卷容器。

如果需要在不同的区域创建 Azure 存储帐户，请参阅 [关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md) 了解逐步说明。

在 Azure 经典门户中的“StorSimple Manager 服务”页上执行以下步骤。

[!INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>使用 PuTTY 连接到设备串行控制台
若要连接到 Windows PowerShell for StorSimple，需要使用终端模拟软件，如 PuTTY。 可以在访问设备时，直接通过串行控制台或从远程计算机上打开 telnet 会话来使用 PuTTY。

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>扫描并应用更新
更新设备可能需要 1-4 个小时。 在设备上执行以下步骤以扫描并应用更新。

> [!NOTE]
> 如果已在 Data 0 以外的网络接口上配置网关，安装更新之前需要先禁用 Data 2 和 Data 3 网络接口。 请转到“设备”>“配置”并禁用 Data 2 和 Data 3 接口。 设备更新之后，应重新启用这些接口。
> 
> 

#### <a name="to-update-your-device"></a>更新设备
1. 在设备“快速启动”页上，单击“设备”。 选择物理设备，单击“维护”，并单击“扫描更新”。  
2. 已创建一个用于扫描可用更新的作业。 如果更新可用，“扫描更新”将更改为“安装更新”。 单击“ **安装更新**”。 系统可能要求在安装更新之前禁用 Data 2 和 Data 3。 必须禁用这些网络接口，否则更新会失败。
3. 将创建更新作业。 通过导航到 **作业**监视更新状态。
   
   > [!NOTE]
   > 更新作业启动时，状态立即显示为 50%。 仅在更新作业完成后，状态才会更改为 100%。 更新过程没有实时状态。
   > 
   > 
4. 成功更新设备后，请启用 Data 2 和 Data 3 网络接口（如果已禁用）。

## <a name="get-the-iqn-of-a-windows-server-host"></a>获取 Windows Server 主机的 IQN
执行以下步骤，获取正在运行 Windows Server 2012 的 Windows 主机的 iSCSI 限定名称 (IQN)。

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>创建手动备份
在 Azure 经典门户中执行以下步骤，在 StorSimple 设备上为单个卷创建按需手动备份。

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="next-steps"></a>后续步骤
* 配置 [虚拟设备](storsimple-virtual-device-u2.md)。
* 使用 [StorSimple Manager 服务](https://msdn.microsoft.com/library/azure/dn772396.aspx) 管理 StorSimple 设备。


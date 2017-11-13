---
title: "部署 StorSimple 设备 (Update 2) | Microsoft Docs"
description: "介绍部署 StorSimple Update 2 设备和服务的步骤和最佳实践。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 7dff0612-617b-4fc8-a3fe-994c24bc7c51
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6107f64ae86af9b0ee873c8313ebba5371737f4d
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-your-on-premises-storsimple-device-update-2"></a>部署本地 StorSimple 设备 (Update 2)

> [!NOTE]
> 已弃用 StorSimple 经典门户。 按弃用计划，StorSimple 设备管理器将自动移至新的 Azure 门户。 你会收到有关此移动的电子邮件和门户通知。 此文档亦将在稍后停用。 若要查看本文中新的 Azure 门户版本，请转到[部署本地 StorSimple 设备 (Update 2)](storsimple-8000-deployment-walkthrough-u2.md)。 任何与移动相关的问题，请参阅[常见问题解答：转移到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。


> [!div class="op_single_selector"]
> * [Update 2 和更高版本 ](storsimple-deployment-walkthrough-u2.md)
> * [Update 1](storsimple-deployment-walkthrough-u1.md)
> * [正式版](storsimple-deployment-walkthrough.md)

## <a name="overview"></a>概述
欢迎使用 Microsoft Azure StorSimple 设备部署。 本部署教程适用于 StorSimple 8000 系列 Update 2。 本系列教程包括 StorSimple 设备的配置清单、配置先决条件和详细配置步骤。

这些教程中的信息均假定已阅读安全预防措施，并已打开 StorSimple 设备包、装入机架，并连接好电缆。 如果仍然需要执行这些任务，请从阅读 [安全预防措施](storsimple-safety.md)开始。 按照设备具体说明，将设备解包、装入机架并连接好电缆。

* [解包、装载机架，并将电缆接到 8100](storsimple-8100-hardware-installation.md)
* [解包、安装机架，将电缆连接到 8600](storsimple-8600-hardware-installation.md)

需要有管理员权限才能完成安装和配置过程。 建议在开始之前查看配置清单。 部署和配置过程可能需要一些时间才能完成。

> [!NOTE]
> Microsoft Azure 网站上发布的 StorSimple 部署信息仅适用于 StorSimple 8000 系列设备。 如需 7000 系列设备的完整信息，请转到： [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。 如需 7000 系列的部署信息，请参阅 [StorSimple 系统快速入门指南](http://onlinehelp.storsimple.com/111_Appliance/)。
> 
> 

## <a name="deployment-steps"></a>部署步骤
执行这些所需的步骤来配置 StorSimple 设备，并将其连接到 StorSimple Manager 服务。 除了这些所需的步骤外，在部署过程中可能还需要完成一些可选步骤和过程。 逐步部署说明将指示何时应执行每个可选步骤。

| 步骤 | 说明 |
| --- | --- |
| **先决条件** |这些是针对即将进行的部署需要做好准备。 |
| [部署配置清单](#deployment-configuration-checklist) |使用此清单来收集和记录部署之前或期间的信息。 |
| [部署先决条件](#deployment-prerequisites) |这些项会验证环境是否已准备就绪以进行部署。 |
|  | |
| **逐步部署** |需要完成这些步骤，以在生产中部署 StorSimple 设备。 |
| [步骤 1：创建新服务](#step-1-create-a-new-service) |设置 StorSimple 设备的云管理和存储。 *如果其他 StorSimple 设备有现有服务，请跳过此步骤*。 |
| [步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key) |使用此密钥来注册 StorSimple 设备，并将其连接到管理服务。 |
| [步骤 3︰通过 Windows PowerShell for StorSimple 配置和注册设备](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |使用管理服务将设备连接到网络，并通过 Azure 注册以完成安装。 |
| [步骤 4：完成最低版本的设备安装](#step-4-complete-minimum-device-setup)</br>[可选：更新 StorSimple 设备](#scan-for-and-apply-updates) |使用管理服务来完成设备安装，并启用以使其能够提供存储。 |
| [步骤 5：创建卷容器](#step-5-create-a-volume-container) |创建容器来预配卷。 卷容器具有其中所包含的所有卷的存储帐户、带宽和加密设置。 |
| [步骤 6：创建卷](#step-6-create-a-volume) |在服务器的 StorSimple 设备上预配存储卷。 |
| [步骤 7：装载、初始化和格式化卷](#step-7-mount-initialize-and-format-a-volume)</br>[可选：配置 MPIO](storsimple-configure-mpio-windows-server.md) |将服务器连接到设备提供的 iSCSI 存储。 根据情况配置 MPIO，以确保服务器可以容许链接、网络和接口故障。 |
| [步骤 8：执行备份](#step-8-take-a-backup) |设置备份策略以保护数据 |
|  | |
| **其他过程** |在部署解决方案时可能需要参阅这些过程。 |
| [针对服务配置新的存储帐户](#configure-a-new-storage-account-for-the-service) | |
| [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console) | |
| [获取 Windows Server 主机的 IQN](#get-the-iqn-of-a-windows-server-host) | |
| [创建手动备份](#create-a-manual-backup) | |

## <a name="deployment-configuration-checklist"></a>部署配置清单
在部署设备之前需要收集信息，以配置 StorSimple 设备上的软件。 提前准备一些此类信息有助于简化环境中 StorSimple 设备的部署过程。 下载并使用此清单，以记下部署设备时的配置详细信息。

* [下载 StorSimple 部署配置清单](http://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>部署先决条件
以下部分说明 StorSimple Manager 服务和 StorSimple 设备配置的先决条件。

### <a name="for-the-storsimple-manager-service"></a>对于 StorSimple Manager 服务
在开始之前，请确保：

* 具有 Microsoft 帐户和访问凭据。
* 具有 Microsoft Azure 存储帐户和访问凭据。
* StorSimple Manager 服务已启用 Microsoft Azure 订阅。 应通过 [企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)购买订阅。
* 有权访问终端模拟软件，如 PuTTY。

### <a name="for-the-device-in-the-datacenter"></a>对于数据中心中的设备
在配置设备前，请确保设备已完全解包、装载在机架上，并已连接所有电源、网络和串行访问线缆，如下所述：

* [解包、装载机架，并将电缆接到 8100 设备](storsimple-8100-hardware-installation.md)
* [解包、装载机架，并电缆接到 8600 设备](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络
在开始之前，请确保：

* 数据中心防火墙的端口已开放，以允许 iSCSI 和云流量，如 [StorSimple 设备的网络要求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)中所述。

## <a name="step-by-step-deployment"></a>逐步部署
使用下面的分步说明在数据中心部署 StorSimple 设备。

## <a name="step-1-create-a-new-service"></a>步骤 1：创建新服务
StorSimple Manager 服务可以管理多个 StorSimple 设备。 执行以下步骤以创建新的 StorSimple Manager 服务实例。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> 如果未启用服务的自动创建存储帐户，则在成功创建服务后，需要创建至少一个存储帐户。 在创建卷容器时会使用此帐户。 
> 
> * 如果未自动创建存储帐户，请转到 [针对服务配置新的存储帐户](#configure-a-new-storage-account-for-the-service) 了解详细说明。 
> * 如果启用了自动创建存储帐户，请转到 [步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)。
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>步骤 2：获取服务注册密钥
在 StorSimple Manager 服务启动后和运行中时，将需要获取服务注册密钥。 此密钥可用来注册 StorSimple 设备，并将其连接到服务。

在管理门户中执行以下步骤。

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>步骤 3︰通过 Windows PowerShell for StorSimple 配置和注册设备
使用 Windows PowerShell for StorSimple 来完成 StorSimple 设备的初始设置，如以下过程所述。 需要使用终端模拟软件来完成此步骤。 有关详细信息，请参阅 [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console)。

[!INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>步骤 4：完成最低版本的设备安装
若要完成 StorSimple 设备的最起码设备配置，需要： 

* 设置备用 DNS 服务器。
* 至少在一个网络接口上启用 iSCSI。
* 将固定的 IP 地址分配给两个控制器。

在管理门户中执行以下步骤，以完成最低版本的设备安装。

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>步骤 5：创建卷容器
卷容器具有其中所包含的所有卷的存储帐户、带宽和加密设置。 需要创建卷容器后才可以开始在 StorSimple 设备上设置卷。 

请在管理门户中执行以下步骤，以创建卷容器。

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>步骤 6：创建卷
创建卷容器后，就可以为服务器在 StorSimple 设备上设置存储卷。 请在管理门户中执行以下步骤，以创建卷。

> [!IMPORTANT]
> StorSimple Manager 可以创建既精简又完全预配的卷。 但是不能创建部分预配的卷。 
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>步骤 7：装载、初始化和格式化卷
请在 Windows Server 主机上执行以下步骤。 

> [!IMPORTANT]
> * 为了获得 StorSimple 解决方案的高可用性，建议在配置 iSCSI 之前先在主机服务器（可选）上配置 MPIO。 主机服务器上的 MPIO 配置将确保服务器可以容许链接、网络或接口故障。
> * 如需 Windows Server 主机上的 MPIO 和 iSCSI 安装和配置说明，请转到 [为 StorSimple 设备配置 MPIO](storsimple-configure-mpio-windows-server.md)。 这些内容还将包括装载、初始化和格式化 StorSimple 卷的步骤。
> * 如需 Linux 主机上的 MPIO 和 iSCSI 安装和配置说明，请转到 [为 StorSimple Linux 主机配置 MPIO](storsimple-configure-mpio-on-linux.md)
> 
> 

如果决定不配置 MPIO，请执行以下步骤来装载、初始化和格式化 Windows Server 主机上的 StorSimple 卷。

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>步骤 8：执行备份
备份可提供卷的时间点保护，并可提高可恢复性，同时最大限度地减少恢复时间。 可以在 StorSimple 设备上执行两种类型的备份：本地快照和云快照。 上述每种备份类型都可以是**计划**或**手动**的。 

请在管理门户中执行以下步骤，以创建计划的备份。

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

可以随时进行手动备份。 如需相关过程，请转到 [创建手动备份](#create-a-manual-backup)。 

## <a name="configure-a-new-storage-account-for-the-service"></a>针对服务配置新的存储帐户
这是一个可选步骤，只有当未启用服务自动创建存储帐户时，才需要执行。 必须要具有 Microsoft Azure 存储帐户才可以创建 StorSimple 卷容器。

如果需要在不同的区域创建 Azure 存储帐户，请参阅 [关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md) 了解逐步说明。

请在 **StorSimple Manager 服务** 页的管理门户中执行以下步骤。

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>使用 PuTTY 连接到设备串行控制台
若要连接到 Windows PowerShell for StorSimple，需要使用终端模拟软件，如 PuTTY。 可以在访问设备时，直接通过串行控制台或从远程计算机上打开 telnet 会话来使用 PuTTY。

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>扫描并应用更新
更新设备可能需要几个小时。 在设备上执行以下步骤以扫描并应用更新。
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>更新设备
1. 在设备“快速启动”页上，单击“设备”。 选择物理设备，单击“维护”，并单击“扫描更新”。  
2. 已创建一个用于扫描可用更新的作业。 如果更新可用，“扫描更新”将更改为“安装更新”。 单击“ **安装更新**”。 
3. 将创建更新作业。 通过导航到 **作业**监视更新状态。
   
   > [!NOTE]
   > 更新作业启动时，状态立即显示为 50%。 仅在更新作业完成后，状态才会更改为 100%。 更新过程没有实时状态。
   > 
   > 
4. 成功更新设备后，请启用 Data 2 和 Data 3 网络接口（如果这些被禁用）。

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>获取 Windows Server 主机的 IQN
请执行以下步骤，获取正在运行 Windows Server® 2012 的 Windows 主机的 iSCSI 限定名称 (IQN)。

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>创建手动备份
请在管理门户执行以下步骤，以在 StorSimple 设备上为单独的卷创建一个按需手动备份。

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="next-steps"></a>后续步骤
* 配置 [虚拟设备](storsimple-virtual-device-u2.md)。
* 使用 [StorSimple Manager 服务](storsimple-manager-service-administration.md) 管理 StorSimple 设备。


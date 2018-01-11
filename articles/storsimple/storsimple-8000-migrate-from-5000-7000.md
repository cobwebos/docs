---
title: "将 StorSimple 5000-7000 系列上的数据迁移到 8000 系列设备 | Microsoft 文档"
description: "概述迁移功能和先决条件。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2017
ms.author: alkohli
ms.openlocfilehash: 36df62c4b01c623702707d39c6af59f4752ee6e0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>将数据从 StorSimple 5000-7000 系列迁移到 8000 系列设备

> [!IMPORTANT]
> - 迁移当前是一项辅助的操作。 如果想要将数据从 StorSimple 5000-7000 系列设备迁移到 8000 系列设备，需要与 Microsoft 支持部门一起制定迁移计划。 然后，Microsoft 支持部门将启用你的迁移订阅。 有关详细信息，请参阅如何[开具支持票证](storsimple-8000-contact-microsoft-support.md)。
> - 提交服务请求文件后，可能需要几周的时间来执行迁移计划和实际启动迁移。
> - 在联系 Microsoft 支持部门前，请确保审阅并完成本文中所示的[迁移先决条件](#migration-prerequisites)。

## <a name="overview"></a>概述

本文介绍可让 StorSimple 5000-7000 系列客户将其数据迁移到 StorSimple 8000 系列物理设备或 8010/8020 云设备的迁移功能。 此外，本文还提供了可下载分步演练的链接，供你详细了解将数据从 5000-7000 系列旧设备迁移到 8000 系列物理或云设备所需的步骤。

本文既适用于本地 8000 系列设备，也适用于 StorSimple 云设备。


## <a name="migration-feature-versus-host-side-migration"></a>迁移功能和主机端迁移的对比

你可以使用迁移功能或通过执行主机端迁移来移动数据。 本部分将介绍每种方法的详细信息，包括各自的优点和缺点。 根据这些信息来确定你想要迁移数据的方法。

迁移功能模拟的是从 7000/5000 系列到 8000 系列的灾难恢复 (DR) 过程。 此功能允许你在 Azure 上将数据从 5000/7000 系列格式迁移到 8000 系列格式。 迁移过程是通过使用 StorSimple 迁移工具启动的。 该工具在 8000 系列设备上启动备份元数据的下载和转换，然后使用最新的备份在设备上公开卷。

|      | 优点                                                                                                                                     |缺点                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | 迁移过程将保留对 5000/7000 系列所进行的备份历史记录。                                               | 当用户尝试访问数据时，此迁移将从 Azure 下载数据，从而产生数据下载成本。                                     |
| 2.   | 在主机端没有迁移的数据。                                                                                                     | 此过程需要在 8000 系列上开始备份和最新备份进行期间具有停机时间（可以使用迁移工具进行预估）。 |
| 3.   | 此过程将保留在 8000 系列设备上的所有策略、带宽模板、加密和其他设置。                      | 用户访问权限仅能获得用户访问的数据，不能解除冻结整个数据集。                                                  |
| 4.   | 此过程需要额外的时间来转换 Azure 中所有的旧备份，该过程是在不影响生产的情况下异步执行的。 | 只能在云配置级别实施迁移操作。  云配置中的各个卷无法进行单独迁移                       |

主机端迁移允许独立设置 8000 系列并将数据从 5000/7000 系列设备复制到 8000 系列设备。 这等同于将数据从一个存储设备迁移到另一个存储设备。 可使用 Diskboss、robocopy 等各种工具来复制数据。

|      | 优点                                                                                                                      |缺点                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | 迁移过程可以按卷来分阶段完成。                                               | 以前的备份（在 5000/7000 系列进行的备份）将不能在 8000 系列设备上使用。                                                                                                       |
| 2.   | 允许在 Azure 上将数据合并到一个存储帐户。                                                       | 因为 8000 系列上的所有数据需要全部备份到 Azure，因此，第一次备份到 8000 系列上的云将需要较长的时间。                                                                     |
| 3.   | 在成功迁移后，所有数据都将迁移到本地设备上。 当访问数据时将不存在延迟。 | Azure 存储空间的使用将会增加，直到将数据从 5000/7000 设备中删除。                                                                                                        |
| 4.   |                                                                                                                           | 如果 7000/5000 系列设备包含大量数据，则在迁移期间，需要从 Azure 下载该数据，而这将产生与从 Azure 中下载数据相关的成本和延迟。 |

本文仅着重介绍从 5000/7000 到 8000 系列设备的迁移功能。 有关主机端迁移的详细信息，请转到[从其他存储设备中迁移](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf)。

## <a name="migration-prerequisites"></a>迁移先决条件

以下是有关旧版 5000 或 7000 系列设备和 8000 系列 StorSimple 设备的迁移先决条件。

> [!IMPORTANT]
> 在向 Microsoft 支持部门提交服务请求时，请先审阅并完成迁移先决条件。

### <a name="for-the-50007000-series-device-source"></a>对于 5000/7000 系列设备（源）

开始迁移前，请确保：

* 具有 5000 或 7000 系列源设备，设备可以是在运行设备或出现故障的设备。

    > [!IMPORTANT]
    > 我们建议在整个迁移过程中能够串行访问该设备。 如果遇到任何设备问题，串行访问将有助于进行故障排除。

* 你的 5000 或 7000 系列源设备正在运行软件版本 v2.1.1.518 或更高版本。 不支持更早的版本。
* 若要验证你的 5000 或 7000 系列正在运行的版本，请查看 Web UI 的右上角。 该位置应显示你的设备正在运行的软件版本。 针对迁移，你的 5000 或 7000 系列应运行 v2.1.1.518。

    ![检查旧设备上的软件版本。](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * 如果你的当前设备运行的不是 v2.1.1.518 或更高版本，请将系统升级到所要求的最低版本。 有关详细说明，请参阅[将系统升级到 v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)。
    * 如果正在运行的是 v2.1.1.518，请转到 Web UI 查看是否存在有关注册表还原失败的任何通知。 如果注册表还原已失败，请运行注册表还原。 有关详细信息，请参阅如何[运行注册表还原](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry)。
    * 如果有出现故障的设备没有运行 v2.1.1.518，请将其故障转移到运行 v2.1.1.518 的替换设备上。 有关详细说明，请参阅 5000/7000 系列 StorSimple 设备的 DR。
    * 通过使用云快照备份设备的数据。
    * 检查在源设备上是否有运行的任何其他活动备份作业。 这包括在 StorSimple 数据保护控制台主机上的作业。 等待当前作业完成。


### <a name="for-the-8000-series-physical-device-target"></a>对于 8000 系列物理设备（目标）

开始迁移前，请确保：

* 你的目标 8000 系列设备已注册并正在运行。 有关详细信息，请参阅如何[使用 StorSimple Manager 服务来部署 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)。
* 你的 8000 系列设备安装有最新的 StorSimple 8000 Series Update 5 并正在运行 6.3.9600.17845 或更高版本。 如果你的设备没有安装最新的更新，则需在继续迁移前先安装最新的更新。 有关详细信息，请参阅如何[在 8000 系列设备上安装最新的更新](storsimple-8000-install-update-5.md)。
* 已对迁移启用 Azure 订阅。 如果你的订阅未能启用，请联系 Microsoft 支持部门以启用迁移的订阅。

### <a name="for-the-80108020-cloud-appliance-target"></a>对于 8010/8020 云设备（目标）

开始迁移前，请确保：

* 你的目标云设备已注册并正在运行。 有关详细信息，请参阅如何[部署和管理 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。
* 你的云设备正在运行最新的 StorSimple 8000 Series Update 5 软件版本 6.3.9600.17845。 如果你的云设备没有运行 Update 5，请在继续迁移前先创建新的 Update 5 云设备。 有关详细信息，请参阅如何[创建 8010/8020 云设备](storsimple-8000-cloud-appliance-u2.md)。

### <a name="for-the-computer-running-storsimple-migration-tool"></a>对于运行 StorSimple 迁移工具的计算机

StorSimple 迁移工具是一个基于 UI 的工具，可用于将数据从 StorSimple 5000-7000 系列迁移到 8000 系列设备。 若要安装 StorSimple 迁移工具，请使用满足以下要求的计算机。

计算机具有 Internet 连接且：

* 正在运行以下操作系统
    * Windows 10。
    * Windows Server 2012 R2（或更高版本），以安装 StorSimple 迁移工具。
* 已安装 .NET 4.5.2。
* 具有至少 5 GB 的可用空间来安装和使用该工具。

> [!TIP]
> 如果你的 StorSimple 设备连接到 Windows Server 主机，则可以在 Windows Server 主机计算机上安装迁移工具。

#### <a name="to-install-storsimple-migration-tool"></a>安装 StorSimple 迁移工具

请执行以下步骤，在你的计算机上安装 StorSimple 迁移工具。

1. 将文件夹 StorSimple8000SeriesMigrationTool 复制到你的 Windows 计算机。 请确保复制软件的驱动器具有足够的空间。

    打开文件夹中的工具配置文件 StorSimple8000SeriesMigrationTool.exe.config。 以下是文件的代码段。
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. 编辑对应于密钥的值，并替换为：

    * `UserName` – 登录到 Azure 门户的用户名。
    * `SubscriptionName and SubscriptionId` - 你的 Azure 订阅的名称和 ID。 在 StorSimple 设备管理器服务登陆页中，单击“常规”下的“属性”。 复制与你的服务相关联的订阅名称和订阅 ID。
    * `ResourceName` – Azure 门户中你的 StorSimple 设备管理器服务的名称。 也会显示在服务属性下。
    * `ResourceGroup` – Azure 门户中与你的 StorSimple 设备管理器服务相关联的资源组的名称。 也会显示在服务属性下。
    ![检查目标设备的服务属性](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` - Azure 门户中的 Azure Active Directory 租户 ID。 以管理员身份登录到 Microsoft Azure。 在 Microsoft Azure 门户中，单击“Azure Active Directory”。 在“管理”下，单击“属性”。 租户 ID 将显示在“目录 ID”框中。
    ![检查 Azure Active Directory 的租户 ID](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  保存对配置文件所做的更改。
4.  运行 StorSimple8000SeriesMigrationTool.exe 以启动工具。 当系统提示输入凭据时，在 Azure 门户中提供与你的订阅相关联的凭据。 
5.  将显示 StorSimple 迁移工具 UI。
  

## <a name="next-steps"></a>后续步骤
下载有关如何[将数据从 StorSimple 5000-7000 系列迁移到 8000 系列设备](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)的分步说明。

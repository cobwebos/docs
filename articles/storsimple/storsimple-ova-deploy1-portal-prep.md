---
title: "部署 StorSimple 虚拟阵列 1 - 门户准备"
description: "此为部署 StorSimple 虚拟阵列的第一个教程，介绍如何准备门户"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 232e765b-9d2c-4907-8b11-aa7e1a17d3df
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 842ea26941a1141cde7db4e6ebff07d52aed9450


---
# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>部署 StorSimple 虚拟阵列 - 准备门户
![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>概述
本文适用于运行 2016 年 3 月公开发行 (GA) 版的 Microsoft Azure StorSimple 虚拟阵列（也称 StorSimple 本地虚拟设备或 StorSimple 虚拟设备）。 这是此系列部署教程的第一篇文章，是将虚拟阵列完全部署为文件服务器或 iSCSI 服务器所必读的。 本文介绍在预配虚拟阵列之前创建和配置 StorSimple Manager 服务所需进行的准备。 本文还提供了部署配置清单和配置先决条件的链接。

需要有管理员权限才能完成安装和配置过程。 建议在开始之前查看部署配置清单。 门户准备只需不到 10 分钟的时间。

本文发布的信息适用于在 Azure 经典门户和 Microsoft Azure 政府云中部署 StorSimple 虚拟阵列的情况。

### <a name="get-started"></a>入门
部署工作流包括：准备门户、在虚拟化环境中预配虚拟阵列，以及完成设置。 开始将 StorSimple 虚拟阵列部署为文件服务器或 iSCSI 服务器之前，需参阅下述表格形式的资源（文章和视频）。

#### <a name="deployment-articles"></a>部署文章
若要部署 StorSimple 虚拟阵列，请按规定顺序参阅以下文章。

| **#** | **此步骤的内容** | **需执行的操作…** | **使用以下文档。** |
| --- | --- | --- | --- |
| 1. |**设置 Azure 经典门户** |在预配 StorSimple 虚拟设备之前创建和配置 StorSimple Manager 服务。 |[准备门户](storsimple-ova-deploy1-portal-prep.md) |
| 2. |**预配虚拟阵列** |对于 Hyper-V，可在主机系统上预配和连接 StorSimple 虚拟设备，该主机系统在 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 上运行 Hyper-V。 <br></br> <br></br> 对于 VMware，可在运行 VMware ESXi 5.5 及更高版本的主机系统上预配和连接 StorSimple 本地虚拟设备。<br></br> |[在 Hyper-V 中预配虚拟阵列](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [在 VMware 中预配虚拟阵列](storsimple-ova-deploy2-provision-vmware.md) |
| 3. |**设置虚拟阵列** |对于文件服务器，请执行初始设置、注册 StorSimple 文件服务器并完成设备设置。 然后即可预配 SMB 共享。 <br></br> <br></br> 对于 iSCSI 服务器，请执行初始设置、注册 StorSimple iSCSI 服务器并完成设备设置。 然后即可预配 iSCSI 卷。 |[将虚拟阵列设置为文件服务器](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[将虚拟阵列设置为 iSCSI 服务器](storsimple-ova-deploy3-iscsi-setup.md) |

#### <a name="deployment-videos"></a>部署视频
| **若要执行此步骤...** | **观看以下视频。** |
| --- | --- |
| StorSimple 虚拟阵列入门分步说明。 |[StorSimple 虚拟阵列入门](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/) |
| 在 Hyper-V 中预配 StorSimple 虚拟阵列的分步说明。 |[创建 StorSimple 虚拟阵列](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
| 配置和注册 StorSimple 虚拟阵列的分步说明 |[配置 StorSimple 虚拟阵列](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/) |
| 在配置为文件服务器的 StorSimple 虚拟阵列上创建共享、备份共享和还原数据的分步说明 |[使用 StorSimple 虚拟阵列](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/) |
| StorSimple 虚拟阵列的故障转移和灾难恢复分步说明 |[StorSimple 虚拟阵列灾难恢复](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/) |

现在可以开始设置 Azure 经典门户。

## <a name="configuration-checklist"></a>配置清单
配置清单描述在配置 StorSimple 设备上的软件之前需要收集的信息。 提前准备此类信息有助于简化环境中 StorSimple 设备的部署过程。 需要以下清单之一，具体取决于是将 StorSimple 虚拟设备部署为文件服务器还是 iSCSI 服务器。

* 下载 [StorSimple 虚拟阵列文件服务器配置清单](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)。
* 下载 [StorSimple 虚拟阵列 iSCSI 服务器配置清单](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)。

## <a name="prerequisites"></a>先决条件
下面是 StorSimple Manager 服务、StorSimple 虚拟设备以及数据中心网络的配置先决条件。

### <a name="for-the-storsimple-manager-service"></a>对于 StorSimple Manager 服务
在开始之前，请确保：

* 具有 Microsoft 帐户和访问凭据。
* 具有 Microsoft Azure 存储帐户和访问凭据。
* 应该为 StorSimple Manager 服务启用 Microsoft Azure 订阅。

### <a name="for-the-storsimple-virtual-device"></a>对于 StorSimple 虚拟设备
在部署虚拟设备之前，请确保：

* 有权访问在 Windows Server 2008 R2 或更高版本上运行 Hyper-V 的主机系统或者运行 VMware（ESXi 5.5 或更高版本）的主机系统，以便预配设备。
* 主机系统能够将以下资源专用于预配虚拟设备：
  
  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。
  * 一个网络接口。
  * 一个 500 GB 的用于系统数据的虚拟磁盘。

### <a name="for-the-datacenter-network"></a>对于数据中心网络
在开始之前，请确保：

* 数据中心的网络按 StorSimple 设备的网络要求配置。 有关详细信息，请参阅 [StorSimple 虚拟阵列系统要求](storsimple-ova-system-requirements.md)。
* StorSimple 虚拟设备始终有专用的 5 Mbps Internet 带宽（或更高带宽）。 此带宽不应与任何其他应用程序共享。

## <a name="step-by-step-preparation"></a>分步准备
按照以下分步说明针对 StorSimple Manager 服务准备门户。

## <a name="step-1-create-a-new-service"></a>步骤 1：创建新服务
StorSimple Manager 服务的单个实例可以管理多个 StorSimple 1200 设备。 执行以下步骤以创建新的 StorSimple Manager 服务实例。 如果已经有 StorSimple Manager 服务来管理 1200 设备，请跳过此步骤，转到[步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)。

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [!IMPORTANT]
> 如果未启用服务的自动创建存储帐户，则在成功创建服务后，需要创建至少一个存储帐户。
> 
> * 如果未自动创建存储帐户，请转到 [针对服务配置新的存储帐户](#optional-step-configure-a-new-storage-account-for-the-service) 了解详细说明。
> * 如果启用了自动创建存储帐户，请转到 [步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)。
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>步骤 2：获取服务注册密钥
在 StorSimple Manager 服务启动后和运行中时，将需要获取服务注册密钥。 此密钥可用来注册 StorSimple 设备，并将其连接到服务。

在 [Azure 经典门户](https://manage.windowsazure.com/)中执行以下步骤。

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [!NOTE]
> 服务注册密钥用于注册需要注册 StorSimple Manager 服务的所有 StorSimple Manager 设备。
> 
> 

## <a name="step-3-download-the-virtual-device-image"></a>步骤 3：下载虚拟设备映像
有了服务注册密钥以后，需下载相应的虚拟设备映像，在主机系统上预配虚拟设备。 虚拟设备映像特定于操作系统，可以从 Azure 经典门户的“快速启动”页下载。

> [!IMPORTANT]
> 在 StorSimple 虚拟阵列上运行的软件只能结合 Storsimple Manager 服务使用。
> 
> 

在 [Azure 经典门户](https://manage.windowsazure.com/)中执行以下步骤。

#### <a name="to-get-the-virtual-device-image"></a>获取虚拟设备映像
1. 在“StorSimple Manager服务”页上，单击创建的服务。 这将使你转到“快速启动”  页。 （可随时单击“快速启动”图标 ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) 访问“快速启动”页。）
2. 单击需要从 Microsoft 下载中心下载的映像的相应链接。 映像文件约为 4.8 GB。
   
   * Windows Server 2012 及更高版本上的适用于 Hyper-V 的 VHDX
   * Windows Server 2008 R2 及更高版本上的适用于 Hyper-V 的 VHD
   * 适用于 VMWare ESXi 5.5 及更高版本的 VMDK
3. 下载该文件并将其解压缩到本地驱动器，记下解压缩文件所在的位置。

![视频图标](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **可用视频**

观看 StorSimple 虚拟阵列入门分步说明的视频。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Get-Started-with-the-StorSimple-Virtual-Array/player]
> 
> 

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>可选步骤：针对服务配置新的存储帐户
此为可选步骤，只需在无法通过服务自动创建存储帐户的情况下执行。

如需在其他区域创建 Azure 存储帐户，请参阅[如何创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)以获取分步说明。

在 Azure 经典门户的“StorSimple Manager 服务”[](https://manage.windowsazure.com/)页上执行以下步骤，添加现有的 Microsoft Azure 存储帐户。

#### <a name="to-add-a-storage-account"></a>添加存储帐户
1. 在 StorSimple Manager 服务登录页上，选择服务并双击。 这将使你转到“快速启动”  页。 选择“配置”  页。
2. 单击“添加/编辑存储帐户” 。 在“添加/编辑存储帐户”  对话框中，执行以下操作：
   
   1. 单击“新增” 。
   2. 为你的存储帐户提供名称。
   3. 为你的 Microsoft Azure 存储帐户提供主“访问密钥”  。
   4. 选择“启用 SSL 模式”，创建用于在设备和云之间进行网络通信的安全通道。 清除“启用 SSL 模式”复选框的前提是在私有云内操作。
   5. 单击选中图标 ![](./media/storsimple-ova-deploy1-portal-prep/image7.png)。 你将收到成功创建存储帐户的通知。
      
      ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)
3. 新创建的存储帐户将显示在“存储帐户”下的“配置”页上。 单击“保存”  ，保存新创建的存储帐户。 提示确认时单击“ **确定** ”。

## <a name="next-step"></a>后续步骤
下一步是针对 StorSimple 虚拟设备预配虚拟机。 根据主机操作系统参阅以下操作的详细说明：

* [在 Hyper-V 中预配 StorSimple 虚拟阵列](storsimple-ova-deploy2-provision-hyperv.md)
* [在 VMware 中预配 StorSimple 虚拟阵列](storsimple-ova-deploy2-provision-vmware.md)




<!--HONumber=Jan17_HO5-->



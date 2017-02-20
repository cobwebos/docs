---
title: "StorSimple 虚拟阵列的门户准备工作 | Microsoft 文档"
description: "此为部署 StorSimple 虚拟阵列的第一个教程，介绍如何准备 Azure 门户"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/03/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 37f2816cb1fa9c7db43359f16669e6521b0fe46a
ms.openlocfilehash: 8539540f96d1a6fbb34736eebb0a5897b4754c9f

---
# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>部署 StorSimple 虚拟阵列 - 准备门户

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>概述

这是此系列部署教程的第一篇文章，是使用 Resource Manager 模型将虚拟阵列完全部署为文件服务器或 iSCSI 服务器所必读的。 本文介绍在预配虚拟阵列之前创建和配置 StorSimple Manager 服务所需进行的准备。 本文还提供了部署配置清单和配置先决条件的链接。

需要有管理员权限才能完成安装和配置过程。 建议在开始之前查看部署配置清单。 门户准备只需不到 10 分钟的时间。

本文发布的信息适用于在 Azure 门户和 Microsoft Azure 政府云中部署 StorSimple 虚拟阵列的情况。

### <a name="get-started"></a>入门
部署工作流包括：准备门户、在虚拟化环境中预配虚拟阵列，以及完成设置。 开始将 StorSimple 虚拟阵列部署为文件服务器或 iSCSI 服务器之前，需参阅下述表格形式的资源。

#### <a name="deployment-articles"></a>部署文章

若要部署 StorSimple 虚拟阵列，请按指定顺序参阅以下文章。

| **#** | **此步骤的内容** | **执行此操作...** | **并使用这些文档。** |
| --- | --- | --- | --- |
| 1. |**设置 Azure 门户** |在预配 StorSimple 虚拟阵列之前创建和配置 StorSimple Device Manager 服务。 |[准备门户](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**预配虚拟阵列** |对于 Hyper-V，可在主机系统上预配和连接 StorSimple 虚拟阵列，该主机系统在 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 上运行 Hyper-V。 <br></br> <br></br> 对于 VMware，可在运行 VMware ESXi 5.5 及更高版本的主机系统上预配和连接 StorSimple 虚拟阵列。<br></br> |[在 Hyper-V 中预配虚拟阵列](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [在 VMware 中预配虚拟阵列](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**设置虚拟阵列** |对于文件服务器，请执行初始设置、注册 StorSimple 文件服务器并完成设备设置。 然后即可预配 SMB 共享。 <br></br> <br></br> 对于 iSCSI 服务器，请执行初始设置、注册 StorSimple iSCSI 服务器并完成设备设置。 然后即可预配 iSCSI 卷。 |[将虚拟阵列设置为文件服务器](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[将虚拟阵列设置为 iSCSI 服务器](storsimple-virtual-array-deploy3-iscsi-setup.md) |

现在可以开始设置 Azure 门户。

## <a name="configuration-checklist"></a>配置清单

配置清单描述在配置 StorSimple 虚拟阵列上的软件之前需要收集的信息。 提前准备此类信息有助于简化环境中 StorSimple 设备的部署过程。 需要以下清单之一，具体取决于是将 StorSimple 虚拟阵列部署为文件服务器还是 iSCSI 服务器。

* 下载 [StorSimple 虚拟阵列文件服务器配置清单](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)。
* 下载 [StorSimple 虚拟阵列 iSCSI 服务器配置清单](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)。

## <a name="prerequisites"></a>先决条件

下面是 StorSimple Device Manager 服务、StorSimple 虚拟阵列以及数据中心网络的配置先决条件。

### <a name="for-the-storsimple-device-manager-service"></a>对于 StorSimple Device Manager 服务

在开始之前，请确保：

* 具有 Microsoft 帐户和访问凭据。
* 具有 Microsoft Azure 存储帐户和访问凭据。
* 应该为 StorSimple Device Manager 服务启用 Microsoft Azure 订阅。

### <a name="for-the-storsimple-virtual-array"></a>对于 StorSimple 虚拟阵列

在部署虚拟阵列之前，请确保：

* 有权访问在 Windows Server 2008 R2 或更高版本上运行 Hyper-V 的主机系统或者运行 VMware（ESXi 5.5 或更高版本）的主机系统，以便预配设备。
* 主机系统能够将以下资源专用于预配虚拟阵列：
  
  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。 如果打算将虚拟阵列配置为文件服务器，8 GB 支持 200 万个文件。 需要 16 GB 的 RAM 才能支持 200-400 万个计划文件。
  * 一个网络接口。
  * 一个 500 GB 的用于系统数据的虚拟磁盘。

### <a name="for-the-datacenter-network"></a>对于数据中心网络

在开始之前，请确保：

* 数据中心的网络按 StorSimple 设备的网络要求配置。 有关详细信息，请参阅 [StorSimple 虚拟阵列系统要求](storsimple-ova-system-requirements.md)。
* StorSimple 虚拟阵列始终有专用的 5 Mbps Internet 带宽（或更高带宽）。 此带宽不应与任何其他应用程序共享。

## <a name="step-by-step-preparation"></a>分步准备

按照以下分步说明针对 StorSimple Device Manager 服务准备门户。

## <a name="step-1-create-a-new-service"></a>步骤 1：创建新服务

StorSimple Device Manager 服务的单个实例可以管理多个 StorSimple 虚拟阵列。 执行以下步骤创建 StorSimple Device Manager 服务实例。 如果使用现有 StorSimple Device Manager 服务来管理虚拟阵列，请跳过此步骤，转到[步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)。

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> 如果未启用服务的自动创建存储帐户，则在成功创建服务后，需要创建至少一个存储帐户。
> 
> * 如果未自动创建存储帐户，请转到 [针对服务配置新的存储帐户](#optional-step-configure-a-new-storage-account-for-the-service) 了解详细说明。
> * 如果启用了自动创建存储帐户，请转到 [步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)。
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>步骤 2：获取服务注册密钥

在 StorSimple Device Manager 服务启动并运行以后，将需要获取服务注册密钥。 此密钥可用来注册 StorSimple 设备，并将其连接到服务。

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> 服务注册密钥用于注册需要注册 StorSimple Device Manager 服务的所有 StorSimple Device Manager 设备。
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>步骤 3：下载虚拟阵列映像

有了服务注册密钥以后，需下载相应的虚拟阵列映像，在主机系统上预配虚拟阵列。 虚拟阵列映像特定于操作系统，可以从 Azure 门户的“快速启动”页下载。

> [!IMPORTANT]
> 在 StorSimple 虚拟阵列上运行的软件只能结合 StorSimple Device Manager 服务使用。
> 
> 

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

#### <a name="to-get-the-virtual-array-image"></a>获取虚拟阵列映像

1. 登录到 [Azure 门户](https://portal.azure.com/)。 
2. 在 Azure 门户中，单击“浏览”>“StorSimple Device Manager”。
3. 选择现有的 StorSimple Device Manager 服务。 在“StorSimple Device Manager”边栏选项卡中，单击“快速启动”。 
4. 单击需要从 Microsoft 下载中心下载的映像的相应链接。 映像文件约为 4.8 GB。
   
   * Windows Server 2012 及更高版本上的适用于 Hyper-V 的 VHDX
   * Windows Server 2008 R2 及更高版本上的适用于 Hyper-V 的 VHD
   * 适用于 VMWare ESXi 5.5 及更高版本的 VMDK
5. 下载该文件并将其解压缩到本地驱动器，记下解压缩文件所在的位置。

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>可选步骤：针对服务配置新的存储帐户

此步骤是可选的，只应在未通过服务启用自动创建存储帐户的情况下执行。

如需在其他区域创建 Azure 存储帐户，请参阅[如何创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)以获取分步说明。

在 [Azure 门户](https://ms.portal.azure.com/)的“StorSimple Device Manager 服务”页上执行以下步骤，添加现有的 Microsoft Azure 存储帐户。

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>添加与 Device Manager 服务具有相同 Azure 订阅的存储帐户凭据

1. 导航到 Device Manager 服务，选择并双击它。 这将打开“概述”边栏选项卡。
2. 在“配置”部分中，选择“存储帐户凭据”。
3. 单击 **“添加”**。
4. 在“添加存储帐户”边栏选项卡中，执行以下操作：
   
    1. 对于“订阅”，选择“当前”。
   
    2. 提供 Azure 存储帐户的名称。
   
    3. 选择“启用”，创建用于在 StorSimple 设备和云之间传输网络通信的安全通道。 仅当要在私有云中操作时，才选择“禁用”。
   
    4. 单击 **“添加”**。 成功创建存储帐户后，你将收到通知。<br></br>
   
     ![添加现有的存储帐户凭据](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>后续步骤

下一步是针对 StorSimple 虚拟阵列预配虚拟机。 根据主机操作系统参阅以下操作的详细说明：

* [在 Hyper-V 中预配 StorSimple 虚拟阵列](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [在 VMware 中预配 StorSimple 虚拟阵列](storsimple-virtual-array-deploy2-provision-vmware.md)




<!--HONumber=Feb17_HO1-->



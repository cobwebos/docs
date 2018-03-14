---
title: "Azure Linux 虚拟机上的附加 VHD 故障排除 | Microsoft Docs"
description: "如何排查 Linux VM 意外重新启动等问题或删除包含附加 VHD 的存储帐户的问题。"
keywords: "ssh 连接被拒绝, ssh 错误, azure ssh, SSH 连接失败"
services: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/28/2018
ms.author: iainfou
ms.openlocfilehash: 10f5e2f4a48d335c6b7d05b5eb5b577765675c0e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-attached-vhds-on-azure-linux-virtual-machines"></a>Azure Linux 虚拟机上的附加 VHD 故障排除

Azure 虚拟机依赖于 OS 磁盘和任何附加数据磁盘的虚拟硬盘 (VHD)。 VHD 作为页 Blob 存储在一个或多个 Azure 存储帐户中。 本文介绍如何排查 VHD 可能会出现的常见问题。 

  * [附加 VHD 的 VM 意外重新启动]
  * [资源管理器部署中的存储删除错误]

## <a name="you-are-experiencing-unexpected-reboots"></a>附加 VHD 的 VM 意外重新启动

如果 Azure 虚拟机 (VM) 具有大量位于同一存储帐户的附加 VHD，则可能会超过单个存储帐户的可伸缩性目标，从而导致 VM 出现故障。 检查存储帐户的分钟指标 (**TotalRequests**/**TotalIngress**/**TotalEgress**)，了解超过存储帐户的可伸缩性目标的峰值。 若要帮助确定是否已对存储帐户进行限制，请参阅“[指标显示 PercentThrottlingError 增加]”一节。

通常，虚拟机对 VHD 进行的每个单独的输入或输出操作都会转换为对基础页 Blob 进行的“Get 页”或“Put 页”操作。 因此，可以根据应用程序的特定行为，对环境使用估计的 IOPS 以优化可以在单个存储帐户中设置的 VHD 数。 Microsoft 建议在单个存储帐户中设置 40 个或更少的磁盘。 有关存储帐户的可伸缩性目标的详细信息（尤其是使用的存储帐户类型的总请求速率和总带宽），请参阅 [Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)。

如果即将超过存储帐户的可伸缩性目标，请将 VHD 放入多个存储帐户中，以减少每个帐户中的活动。

## <a name="storage-delete-errors-in-rm"></a>资源管理器部署中的存储删除错误

借助本部分的故障排除指南，可以了解在 Azure 资源管理器部署中尝试删除 Azure 存储帐户、容器或 blob 时，如何排查出现的下列错误之一。

>**无法删除存储帐户“StorageAccountName”。错误：正在使用存储帐户的项目，因此无法删除该存储帐户。**

>**无法删除 # 个容器(共 # 个):<br>vhds:目前容器上有租用，但请求中未指定任何租用 ID。**

>**无法删除 # 个 blob (共 # 个):<br>BlobName.vhd:目前 blob 上有租用，但请求中未指定任何租用 ID。**

Azure VM 中使用的 VHD 是在 Azure 的标准或高级存储帐户中作为页 Blob 存储的 .vhd 文件。  若要详细了解 Azure 磁盘，请单击[此处](../../virtual-machines/windows/about-disks-and-vhds.md)。 Azure 阻止删除附加到 VM 的磁盘，以防发生损坏。 它还会阻止删除包含附加到 VM 的页 blob 的容器和存储帐户。 

如果收到上述错误之一，请遵循以下过程，删除存储帐户、容器或 blob： 
1. [发现附加到 VM 的 blob](#step-1-identify-blobs-attached-to-a-vm)
2. [删除附加有 OS 磁盘的 VM](#step-2-delete-vm-to-detach-os-disk)
3. [将所有数据磁盘与剩余 VM 拆离](#step-3-detach-data-disk-from-the-vm)

完成上述步骤后，重试删除存储帐户、容器或 blob。

### <a name="step-1-identify-blob-attached-to-a-vm"></a>第 1 步：发现附加到 VM 的 blob

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>方案 1：删除 blob - 发现附加 blob 的 VM
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“所有资源”。 转到存储帐户，在“Blob 服务”下，选择“容器”，并导航到要删除的 blob。
3. 如果 blob 的“租用状态”为“已租用”，则右键单击并选择“编辑元数据”，打开“blob 元数据”窗格。 

    ![门户的屏幕截图，其中突出显示了存储帐户 blob 和右键单击后出现的“编辑元数据”](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. 在“blob 元数据”窗格中，查看并记录 MicrosoftAzureCompute_VMName 的值。 此值是 VHD 附加到的 VM 名称。 （如果看不到此字段，请参阅“重要提示”）
5. 在“blob 元数据”窗格中，查看并记录 MicrosoftAzureCompute_DiskType 的值。 此值确定了附加磁盘是 OS 磁盘，还是数据磁盘（如果此字段不存在，请参阅“重要提示”）。 

     ![门户屏幕截图，其中打开了存储“blob 元数据”窗格](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. 如果 blob 磁盘类型为 OSDisk，请按照[第 2 步：删除 VM 以拆离 OS 磁盘](#step-2-delete-vm-to-detach-os-disk)操作。 否则，如果 blob 磁盘类型为 DataDisk，请按照[第 3 步：将数据磁盘与 VM 拆离](#step-3-detach-data-disk-from-the-vm)操作。 

> [!IMPORTANT]
> 如果 blob 元数据中没有 MicrosoftAzureCompute_VMName 和 MicrosoftAzureCompute_DiskType，表明 blob 为显式租用，未附加到 VM。 必须先中断租用，然后才能删除租用的 blob。 若要中断租用，请右键单击 blob 并选择“中断租用”。 如果租用的 blob 未附加到 VM，则会阻止删除 blob，但不会阻止删除容器或存储帐户。

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>方案 2：删除容器 - 发现容器内附加到 VM 的所有 blob
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“所有资源”。 转到存储帐户，在“Blob 服务”下，选择“容器”，并找到要删除的容器。
3. 单击打开容器，列出容器内的 blob。 在列表中发现“blob 类型”为“页 blob”且“租用状态”为“已租用”的所有 blob。 按照[方案 1](#step-1-identify-blobs-attached-to-a-vm) 操作，发现与其中每个 blob 相关联的 VM。

    ![门户屏幕截图，其中突出显示了存储帐户 blob 和值为“已租用”的“租用状态”](./media/troubleshoot-vhds/utd-disks-sm.png)

4. 按照[第 2 步](#step-2-delete-vm-to-detach-os-disk)和[第 3 步](#step-3-detach-data-disk-from-the-vm)操作，删除附加有 OSDisk 的 VM，并拆离 DataDisk。 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>方案 3：删除存储帐户 - 发现存储帐户内附加到 VM 的所有 blob
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“所有资源”。 转到存储帐户，在“blob 服务”下，选择“容器”。

    ![门户屏幕截图，其中突出显示了存储帐户容器和值为“已租用”的“租用状态”](./media/troubleshoot-vhds/utd-containers-sm.png)

3. 在“容器”窗格中，找到“租用状态”为“已租用”的所有容器，并按照[方案 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) 对每个状态为“已租用”的容器执行操作。
4. 按照[第 2 步](#step-2-delete-vm-to-detach-os-disk)和[第 3 步](#step-3-detach-data-disk-from-the-vm)操作，删除附加有 OSDisk 的 VM，并拆离 DataDisk。 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>第 2 步：删除 VM 以拆离 OS 磁盘
如果 VHD 为 OS 磁盘，必须先删除 VM，然后才能删除附加的 VHD。 完成以下步骤后，无需对附加到同一 VM 的数据磁盘执行其他任何操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“虚拟机”。
3. 选择 VHD 附加到的 VM。
4. 确保没有任何对象正主动使用该虚拟机，并确保不再需要该虚拟机。
5. 在“虚拟机详细信息”窗格顶部，选择“删除”，再单击“是”进行确认。
6. 应删除 VM，但可以保留 VHD。 不过，VHD 不得再附加到 VM，也不得再有租用。 可能需要几分钟才能释放租约。 若要验证租用是否已解除，请转到 blob 位置，再检查“blob 属性”窗格中的“租用状态”是否为“可租用”。

### <a name="step-3-detach-data-disk-from-the-vm"></a>第 3 步：将数据磁盘与 VM 拆离
如果 VHD 是数据磁盘，请从 VM 分离 VHD 以删除租约：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“虚拟机”。
3. 选择 VHD 附加到的 VM。
4. 在“虚拟机详细信息”窗格中，选择“磁盘”。
5. 选择 VHD 附加到的要删除的数据磁盘。 可以检查 VHD 的 URL，从而确定磁盘中附加的 blob。
6. 可以单击磁盘来检查“VHD URI”字段中的路径，从而验证 blob 位置。
7. 选择“磁盘”窗格顶部的“编辑”。
8. 单击要删除的数据磁盘的拆离图标。

     ![门户屏幕截图，其中打开了存储“blob 元数据”窗格](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. 选择“保存”。 此时该磁盘将与 VM 分离，并且 VHD 将不再租用。 可能需要几分钟才能释放租约。 若要验证租用是否已解除，请转到 blob 位置，再检查“blob 属性”窗格中的“租用状态”值是否为“已解锁”或“可租用”。



[附加 VHD 的 VM 意外重新启动]: #you-are-experiencing-unexpected-reboots
[资源管理器部署中的存储删除错误]: #storage-delete-errors-in-rm
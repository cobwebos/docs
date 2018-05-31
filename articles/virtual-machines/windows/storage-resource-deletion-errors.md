---
title: 在 Windows VM 上对 Azure 资源管理器部署中的存储资源删除错误进行故障排除 | Microsoft Docs
description: 在删除含有附加 VHD 的存储资源时如何进行故障排除。
keywords: ssh 连接被拒绝, ssh 错误, azure ssh, SSH 连接失败
services: virtual-machines-windows
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: ba2876919540d4d6c0853ca1e8bf0d88855b951b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364533"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>排查存储资源删除错误

在某些情况下，在 Azure 资源管理器部署中尝试删除 Azure 存储帐户、容器或 blob 时，可能会遇到下列错误之一：

>**无法删除存储帐户“StorageAccountName”。错误：正在使用存储帐户的项目，因此无法删除该存储帐户。**

>**无法删除 # 个容器(共 # 个):<br>vhds:目前容器上有租用，但请求中未指定任何租用 ID。**

>**无法删除 # 个 blob (共 # 个):<br>BlobName.vhd:目前 blob 上有租用，但请求中未指定任何租用 ID。**

Azure VM 中使用的 VHD 是在 Azure 的标准或高级存储帐户中作为页 Blob 存储的 .vhd 文件。 有关 Azure 磁盘的详细信息，请参阅[有关 Microsoft Azure Windows VM 的非托管和托管磁盘存储](about-disks-and-vhds.md)。 

Azure 阻止删除附加到 VM 的磁盘，以防发生损坏。 它还会阻止删除包含附加到 VM 的页 blob 的容器和存储帐户。 

如果收到上述错误之一，请遵循以下过程，删除存储帐户、容器或 blob： 
1. [发现附加到 VM 的 blob](#step-1-identify-blobs-attached-to-a-vm)
2. [删除附加有 OS 磁盘的 VM](#step-2-delete-vm-to-detach-os-disk)
3. [将所有数据磁盘与剩余 VM 拆离](#step-3-detach-data-disk-from-the-vm)

完成上述步骤后，重试删除存储帐户、容器或 blob。

## <a name="step-1-identify-blob-attached-to-a-vm"></a>第 1 步：发现附加到 VM 的 blob

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>方案 1：删除 blob - 发现附加 blob 的 VM
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

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>方案 2：删除容器 - 发现容器内附加到 VM 的所有 blob
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“所有资源”。 转到存储帐户，在“Blob 服务”下，选择“容器”，并找到要删除的容器。
3. 单击打开容器，列出容器内的 blob。 在列表中发现“blob 类型”为“页 blob”且“租用状态”为“已租用”的所有 blob。 按照[方案 1](#step-1-identify-blobs-attached-to-a-vm) 操作，发现与其中每个 blob 相关联的 VM。

    ![门户屏幕截图，其中突出显示了存储帐户 blob 和值为“已租用”的“租用状态”](./media/troubleshoot-vhds/utd-disks-sm.png)

4. 按照[第 2 步](#step-2-delete-vm-to-detach-os-disk)和[第 3 步](#step-3-detach-data-disk-from-the-vm)操作，删除附加有 OSDisk 的 VM，并拆离 DataDisk。 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>方案 3：删除存储帐户 - 发现存储帐户内附加到 VM 的所有 blob
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“所有资源”。 转到存储帐户，在“blob 服务”下，选择“容器”。

    ![门户屏幕截图，其中突出显示了存储帐户容器和值为“已租用”的“租用状态”](./media/troubleshoot-vhds/utd-containers-sm.png)

3. 在“容器”窗格中，找到“租用状态”为“已租用”的所有容器，并按照[方案 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) 对每个状态为“已租用”的容器执行操作。
4. 按照[第 2 步](#step-2-delete-vm-to-detach-os-disk)和[第 3 步](#step-3-detach-data-disk-from-the-vm)操作，删除附加有 OSDisk 的 VM，并拆离 DataDisk。 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>第 2 步：删除 VM 以拆离 OS 磁盘
如果 VHD 为 OS 磁盘，必须先删除 VM，然后才能删除附加的 VHD。 完成以下步骤后，无需对附加到同一 VM 的数据磁盘执行其他任何操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“虚拟机”。
3. 选择 VHD 附加到的 VM。
4. 确保没有任何对象正主动使用该虚拟机，并确保不再需要该虚拟机。
5. 在“虚拟机详细信息”窗格顶部，选择“删除”，再单击“是”进行确认。
6. 应删除 VM，但可以保留 VHD。 不过，VHD 不得再附加到 VM，也不得再有租用。 可能需要几分钟才能释放租约。 若要验证租用是否已解除，请转到 blob 位置，再检查“blob 属性”窗格中的“租用状态”是否为“可租用”。

## <a name="step-3-detach-data-disk-from-the-vm"></a>第 3 步：将数据磁盘与 VM 拆离
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

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm


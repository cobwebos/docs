---
title: 排查删除 Azure 经典存储帐户、容器或 VHD 时发生的错误 | Microsoft Docs
description: 在删除含有附加 VHD 的存储资源时如何进行故障排除。
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078153"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>排查经典存储资源删除错误
请遵循本文中的指导排查在尝试删除 Azure 存储帐户、容器或 *.vhd 页 Blob 文件时发生的以下错误之一。 


本文仅涵盖经典存储资源的问题。 如果用户使用 Azure 门户、PowerShell 或 CLI 删除经典虚拟机，则磁盘不会自动删除。 用户可以使用相应的选项来删除“磁盘”资源。 如果未选择该选项，则“磁盘”资源会阻止删除存储帐户、容器和实际的 *.vhd 页 Blob 文件。

若要详细了解 Azure 磁盘，请单击[此处](../../virtual-machines/windows/managed-disks-overview.md)。 Azure 阻止删除附加到 VM 的磁盘，以防发生损坏。 它还会阻止删除包含附加到 VM 的页 Blob 的容器和存储帐户。 

## <a name="what-is-a-disk"></a>什么是“磁盘”？
“磁盘”资源用于将 *.vhd 页 Blob 文件作为 OS 磁盘或数据磁盘装载到虚拟机。 OS 磁盘或数据磁盘资源在删除之前，会一直在 *.vhd 文件中保留一个租约。 如果“磁盘”资源指向下图所示路径中的任一存储资源，则无法删除此存储资源。

![门户屏幕截图，其中显示（经典）磁盘的“属性”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>删除经典虚拟机的步骤 
1. 删除经典虚拟机。
2. 如果已选中“磁盘”复选框，则与页 Blob *.vhd 关联的**磁盘租约**（如上图所示）将会中断。 实际的页 Blob *.vhd 文件仍在存储帐户中存在。
![门户屏幕截图，其中显示（经典）虚拟机的“删除”错误窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. 中断磁盘租约后，可以删除页 Blob 本身。 删除存储帐户或容器中的所有“磁盘”资源后，可以删除该存储帐户或容器。

>[!NOTE] 
>如果用户删除 VM 但不删除 VHD，页 Blob *.vhd 文件仍会产生存储费用。 费用根据存储帐户类型计算，有关更多详细信息，请查看[定价页](https://azure.microsoft.com/en-us/pricing/details/storage/)。 如果用户不再打算使用 VHD，请将其删除以免将来产生费用。 

## <a name="unable-to-delete-storage-account"></a>无法删除存储帐户 

当用户尝试删除不再需要的经典存储帐户时，可能会看到以下行为。

#### <a name="azure-portal"></a>Azure 门户 
当用户在 [Azure 门户](https://portal.azure.com)中导航到经典存储帐户并单击“删除”时，会看到以下消息： 

磁盘“已附加”到虚拟机

![门户屏幕截图，其中显示（经典）虚拟机的“删除”错误窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


磁盘“未附加”到虚拟机

![门户屏幕截图，其中显示（经典）虚拟机的非“删除”错误窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
用户尝试通过经典 PowerShell cmdlet 删除不再使用的存储帐户。 用户会看到以下消息：

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount :BadRequest:存储帐户 myclassicaccount 包含一些活动的映像和/或磁盘，例如  
> myclassicaccount。 删除此存储帐户前，请确保删除这些映像和/或磁盘。</span>

## <a name="unable-to-delete-storage-container"></a>无法删除存储容器

当用户尝试删除不再需要的经典存储 Blob 容器时，可能会看到以下行为。

#### <a name="azure-portal"></a>Azure 门户 
如果存在指向容器中 *.vhd 页 Blob 文件的“磁盘”租约，则 Azure 门户不允许用户删除该容器。 这是设计使然，目的是防止意外删除包含磁盘租约的 vhd(s) 文件。 

![门户屏幕截图，其中显示存储容器“列表”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
如果用户选择使用 PowerShell 进行删除，则会导致以下错误。 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer :远程服务器返回错误：(412)容器中当前包含租约，但请求中未指定任何租约 ID。HTTP 状态代码：412 - HTTP 错误消息：容器中当前包含租约，但请求中未指定任何租约 ID。</span>

## <a name="unable-to-delete-a-vhd"></a>无法删除 VHD 

删除 Azure 虚拟机后，用户尝试删除 vhd 文件（页 Blob）时收到以下消息：

#### <a name="azure-portal"></a>Azure 门户 
在门户中，可能会根据选择删除的 Blob 列表提供两种体验。

1. 如果仅选择“已租用”Blob，则不会显示“删除”按钮。
![门户屏幕截图，其中显示容器 Blob“列表”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. 如果同时选择了“已租用”和“可用”Blob，则会显示“删除”按钮。 但是，“删除”操作会留下包含磁盘租约的页 Blob。 
![门户屏幕截图，其中显示容器 Blob“列表”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![门户屏幕截图，其中显示选定 Blob 的“删除”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
如果用户选择使用 PowerShell 进行删除，则会导致以下错误。 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob :远程服务器返回错误：(412) Blob 中当前包含租约，但请求中未指定任何租约 ID。HTTP 状态代码：412 - HTTP 错误消息：Blob 中当前包含租约，但请求中未指定任何租约 ID。</span>


## <a name="resolution-steps"></a>解决步骤

### <a name="to-remove-classic-disks"></a>删除经典磁盘
在 Azure 门户中执行以下步骤：
1.  导航到 [Azure 门户](https://portal.azure.com)。
2.  导航到“磁盘(经典)”。 
3.  单击“磁盘”选项卡。![门户屏幕截图，其中显示容器 Blob“列表”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  选择数据磁盘，并单击“删除磁盘”。
 ![门户屏幕截图，其中显示容器 Blob“列表”窗格已打开](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  重试先前失败的“删除”操作。
6.  只要存储帐户或容器包含单个磁盘，就无法将其删除。

### <a name="to-remove-classic-images"></a>删除经典映像   
在 Azure 门户中执行以下步骤：
1.  导航到 [Azure 门户](https://portal.azure.com)。
2.  导航到“OS 映像(经典)”。
3.  删除该映像。
4.  重试先前失败的“删除”操作。
5.  只要存储帐户或容器包含单个映像，就无法将其删除。

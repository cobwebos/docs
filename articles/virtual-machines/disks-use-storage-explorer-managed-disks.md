---
title: 使用 Azure 存储资源管理器管理 Azure 托管磁盘
description: 了解如何使用 Azure 存储资源管理器跨区域上传、下载和迁移 Azure 托管磁盘并创建托管磁盘的快照。
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88749380"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>使用 Azure 存储资源管理器管理 Azure 托管磁盘

存储资源管理器 1.10.0 可让用户上传、下载和复制托管磁盘，以及创建快照。 由于具有这些附加功能，存储资源管理器使你能够将数据从本地迁移到 Azure，并可以跨 Azure 区域迁移数据。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：
- Azure 订阅
- 一个或多个 Azure 托管磁盘
- 最新版本的 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

如果存储资源管理器未连接到 Azure，则将无法使用它来管理资源。 本部分介绍如何将存储资源管理器连接到 Azure 帐户，以便可以使用它来管理资源。

1. 启动 Azure 存储资源管理器并单击左侧的**插件**图标。

    ![单击插件图标](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. 选择“添加 Azure 帐户”，然后单击“下一步”。  

    ![添加 Azure 帐户](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. 在“Azure 登录”对话框中输入 Azure 凭据。 

    ![Azure 登录对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 从列表中选择你的订阅，然后单击“应用”  。

    ![选择订阅](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>从本地 VHD 上传托管磁盘

1. 在左窗格中，展开“磁盘”并选择要将磁盘上传到的资源组。 

    ![选择资源组 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 选择“上传”。 

    ![选择“上传”](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. 在“上传 VHD”中，指定源 VHD、磁盘名称、OS 类型、要将磁盘上传到的区域，以及帐户类型。  对于支持可用性区域的某些地区，可以选择所选的区域。
1. 选择“创建”开始上传磁盘。 

    ![上传 VHD 对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 现在，上传状态会显示在“活动”中。 

    ![上传状态](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 如果上传已完成但右窗格中未显示该磁盘，请选择“刷新”。 

## <a name="download-a-managed-disk"></a>下载托管磁盘

以下步骤说明如何将托管磁盘下载到本地 VHD。 只能下载状态为“未附加”的磁盘，而无法下载状态为“已附加”的磁盘。  

1. 在左窗格中展开“磁盘”（如果尚未展开），并选择要从中下载磁盘的资源组。 

    ![选择资源组 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右窗格中选择要下载的磁盘。
1. 选择“下载”，然后选择磁盘的保存位置。 

    ![下载托管磁盘](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. 选择“保存”，随即会开始下载磁盘。  下载状态将显示在“活动”中。 

    ![下载状态](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>复制托管磁盘

使用存储资源管理器可以在区域内部或跨区域复制托管磁盘。 若要复制磁盘：

1. 在左侧的“磁盘”下拉列表中，选择包含要复制的磁盘的资源组。 

    ![选择资源组 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右窗格中选择要复制的磁盘，然后选择“复制”。 

    ![复制托管磁盘](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 在左窗格中，选择要将磁盘粘贴到的资源组。

    ![选择资源组 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 在右窗格中选择“粘贴”。 

    ![粘贴托管磁盘](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. 在“粘贴磁盘”对话框中填写值。  还可以指定受支持地区中的可用性区域。

    ![“粘贴磁盘”对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. 选择“粘贴”，磁盘随即开始复制，复制状态会显示在“活动”中。  

    ![复制粘贴状态](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>创建快照

1. 在左侧的“磁盘”下拉列表中，选择要创建快照的磁盘所在的资源组。 

    ![选择资源组 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右侧选择要创建快照的磁盘，然后选择“创建快照”。 

    ![创建快照](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. 在“创建快照”中，指定快照名称，以及要在其中创建快照的资源组。  然后选择“创建”  。

    ![“创建快照”对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 创建快照后，可以在“活动”中选择“在门户中打开”，以便在 Azure 门户中查看快照。  

    ![在门户中打开快照](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>后续步骤


了解如何[使用 Azure 门户基于 VHD 创建 VM](windows/create-vm-specialized-portal.md)。

了解如何[使用 Azure 门户将托管数据磁盘附加到 Windows VM](windows/attach-managed-disk-portal.md)。
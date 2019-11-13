---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013771"
---
存储资源管理器1.10.0 使用户能够上传、下载和复制托管磁盘，还可以创建快照。 由于这些附加功能，你可以使用存储资源管理器将数据从本地迁移到 Azure，并在 Azure 区域之间迁移数据。

## <a name="prerequisites"></a>先决条件

若要完成本文，你将需要以下各项：
- Azure 订阅
- 一个或多个 Azure 托管磁盘
- 最新版本的[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

如果存储资源管理器未连接到 Azure，则将无法使用它来管理资源。 本部分介绍如何将其连接到 Azure 帐户，以便可以使用存储资源管理器管理资源。

1. 启动 Azure 存储资源管理器并单击左侧的**插件**图标。

    ![单击插件图标](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. 选择 "**添加 Azure 帐户**"，然后单击 "**下一步**"。

    ![添加 Azure 帐户](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. 在 " **Azure 登录**" 对话框中，输入 azure 凭据。

    ![Azure 登录对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 从列表中选择你的订阅，然后单击“应用”。

    ![选择订阅](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>从本地 VHD 上传托管磁盘

1. 在左侧窗格中，展开 "**磁盘**"，然后选择要将磁盘上传到的资源组。

    ![选择资源组1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 选择“上传”。

    ![选择“上传”](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. 在 "**上载 vhd** " 中，指定源 VHD、磁盘名称、操作系统类型、要将磁盘上传到的区域，以及帐户类型。 在某些区域中支持的可用性区域中，你可以选择所选的区域。
1. 选择 "**创建**" 以开始上传磁盘。

    ![上传 vhd 对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 此时，上传的状态将显示在**活动**中。

    ![上传状态](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 如果上传已完成，但在右窗格中看不到该磁盘，请选择 "**刷新**"。

## <a name="download-a-managed-disk"></a>下载托管磁盘

以下步骤说明如何将托管磁盘下载到本地 VHD。 磁盘的状态**必须为 "** 未连接" 才能下载，无法下载**附加**的磁盘。

1. 在左侧窗格中，如果尚未展开，请展开 "**磁盘**"，然后选择要从中下载磁盘的资源组。

    ![选择资源组1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右侧窗格中，选择要下载的磁盘。
1. 选择 "**下载**"，然后选择要保存磁盘的位置。

    ![下载托管磁盘](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. 选择 "**保存**"，磁盘将开始下载。 下载的状态将显示在**活动**中。

    ![下载状态](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>复制托管磁盘

使用存储资源管理器，可以在区域内或跨区域复制托管磁盘。 复制磁盘：

1. 从左侧的 "**磁盘**" 下拉列表中，选择包含要复制的磁盘的资源组。

    ![选择资源组1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右侧窗格中，选择要复制的磁盘，然后选择 "**复制**"。

    ![复制托管磁盘](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 在左侧窗格中，选择要粘贴磁盘的资源组。

    ![选择资源组2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 选择右窗格中的 "**粘贴**"。

    ![粘贴托管磁盘](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. 在 "**粘贴磁盘**" 对话框中，填写值。 你还可以在受支持的区域中指定可用性区域。

    ![粘贴磁盘对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. 选择 "**粘贴**" 将开始复制磁盘，状态显示在 "**活动**" 中。

    ![复制粘贴状态](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>创建快照

1. 从左侧的 "**磁盘**" 下拉列表中，选择包含要进行快照的磁盘的资源组。

    ![选择资源组1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右侧选择要快照的磁盘，然后选择 "**创建快照**"。

    ![创建快照](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. 在 "**创建快照**" 中，指定快照的名称以及要在其中创建资源组的资源组。 然后，选择“创建”。

    ![创建快照对话框](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 创建快照后，可以在 "**活动**" 中选择 "在**门户中打开**" 以在 Azure 门户中查看快照。

    ![在门户中打开快照](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>后续步骤

---
title: 教程：验证从 Azure Data Box Disk 上传到存储帐户的数据 | Microsoft Docs
description: 使用本教程来了解如何验证从 Azure Data Box Disk 上传到 Azure 存储帐户的数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/22/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: e36f009422307b3b70091775d2288ee710839172
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013992"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>教程：验证从 Azure Data Box Disk 上传的数据

这是本系列的最后一个教程：部署 Azure Data Box Disk。 本教程的内容：

> [!div class="checklist"]
> * 验证 Azure 中的数据上传
> * 从 Data Box 磁盘中擦除数据

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成[教程：退回 Azure Data Box Disk](data-box-disk-deploy-picked-up.md)。


## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

承运人提取磁盘后，门户中的订单状态将更新为“已提货”。  此外还会显示一个跟踪 ID。

![磁盘已提货](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

当 Microsoft 接收和扫描磁盘时，作业状态将更新为“已接收”。  

![磁盘已接收](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

将磁盘连接到 Azure 数据中心的服务器后，会自动复制数据。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。 

![数据复制完成](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

如果复制完成但出现错误，请参阅[排查上传错误](data-box-disk-troubleshoot-upload.md)。

从源中删除数据之前，请确认数据已存储在存储帐户中。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

  - 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - 对于 Azure 文件：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。

  - 如果在 Azure 中成功复制到托管磁盘，则可转到 Azure 门户中的“订单详细信息”，记下为托管磁盘指定的资源组  。

      ![查看订单详细信息](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    转到所记录的资源组，并找到你的托管磁盘。

      ![托管磁盘资源组](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - 如果复制了 VHDX 或动态/差异 VHD，则 VHDX/VHD 会作为块 blob 上传到临时存储帐户。 请转到临时存储帐户 > Blob，然后选择响应的容器 - StandardSSD、StandardHDD 或 PremiumSSD  。 VHDX/VHD 应会在临时存储帐户中显示为块 blob。

若要验证数据是否已上传到 Azure，请执行以下步骤：

1. 转到与磁盘订单关联的存储帐户。
2. 转到“Blob 服务”>“浏览 Blob”。  此时会显示容器列表。 在存储帐户中，将创建与 *BlockBlob* 和 *PageBlob* 文件夹下的子文件夹同名的容器。
    如果文件夹名称不符合 Azure 命名约定，则无法将数据上传到 Azure。

4. 若要验证是否已加载整个数据集，请使用 Microsoft Azure 存储资源管理器。 附加对应于磁盘租赁订单的存储帐户，然后查看 Blob 容器列表。 选择容器，然后依次单击“...更多”、“文件夹统计信息”。   “活动”窗格中会显示该文件夹的统计信息，包括 Blob 数目和 Blob 总大小。  以字节表示的 Blob 总大小应与数据集大小匹配。

    ![存储资源管理器中的文件夹统计信息](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>从 Data Box 磁盘中擦除数据

完成复制并已验证数据位于 Azure 存储帐户中后，请根据 NIST 标准安全擦除磁盘。

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 验证 Azure 中的数据上传
> * 从 Data Box 磁盘中擦除数据


转到下一篇操作指南，了解如何通过 Azure 门户管理 Data Box 磁盘。

> [!div class="nextstepaction"]
> [使用 Azure 门户管理 Azure Data Box 磁盘](./data-box-portal-ui-admin.md)

::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

将数据上传到 Azure 后，从源中删除数据之前，请确认数据已存储在存储帐户中。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

    - **对于块 blob 和页 blob**： https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **对于 Azure 文件存储**： https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。

::: zone-end



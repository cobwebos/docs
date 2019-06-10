---
title: 使用 Azure Data Box Heavy 将文件共享内容迁移到 SharePoint Online | Microsoft Docs
description: 本教程介绍如何使用 Azure Data Box Heavy 将文件共享内容迁移到 SharePoint Online
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: alkohli
ms.openlocfilehash: 5628a1b3ea42c91f49f78699c37bb2b306275e9e
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730825"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>使用 Azure Data Box Heavy 将文件共享内容迁移到 SharePoint Online

使用 Azure Data Box Heavy 和 SharePoint 迁移工具 (SPMT) 可以轻松将文件共享内容迁移到 SharePoint Online 和 OneDrive。 使用 Data Box Heavy 就无需依赖于广域网 (WAN) 链接来传输数据。

Microsoft Azure Data Box 服务可你通过 Microsoft Azure 门户订购设备。 然后，可将服务器中 TB 量级的数据复制到该设备。 将该设备寄回 Microsoft 之后，你的数据将复制到 Azure。 根据传输的数据大小，可以选择：

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview)：每笔订单提供 35-TB 的可用容量用于传输小中型数据集。
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview)：为每个设备提供 80-TB 的可用容量用于传输中大型数据集。
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview)：为每个设备提供 770-TB 的可用容量用于传输大型数据集。 Data Box Heavy 目前为预览版。

本文专门介绍如何使用 Data Box Heavy 将文件共享内容迁移到 SharePoint Online。

## <a name="requirements-and-costs"></a>要求和费用

### <a name="for-data-box-heavy"></a>Data Box Heavy

- Data Box Heavy 仅适用于企业协议 (EA)、云解决方案提供商 (CSP) 或 Azure 赞助产品/服务。 如果你的订阅不属于上述任何类型，请联系 Microsoft 支持部门升级订阅，或查看 [Azure 订阅定价](https://azure.microsoft.com/pricing/)。
- 使用 Data Box Heavy 会产生费用。 请确保查看 [Data Box Heavy 定价](https://azure.microsoft.com/pricing/details/databox/heavy/)。


### <a name="for-sharepoint-online"></a>SharePoint Online

- 请查看 [SharePoint 迁移工具 (SPMT) 的最低要求](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)。

## <a name="workflow-overview"></a>工作流概述

此工作流要求在 Azure Data Box Heavy 以及 SharePoint Online 上执行步骤。
以下步骤与 Azure Data Box Heavy 相关。

1. 订购 Azure Data Box Heavy。
2. 接收并设置设备。
3. 将本地文件共享中的数据复制到设备上的“Azure 文件”文件夹。
4. 复制完成后，根据指示寄回设备。
5. 等待数据完全上传到 Azure。

以下步骤与 SharePoint Online 相关。

6. 在 Azure 门户中创建一个 VM，并在其上装载 Azure 文件共享。
7. 在 Azure VM 上安装 SPMT 工具。
8. 使用 Azure 文件共享作为源运行 SPMT 工具。 
9. 完成该工具的最后一个步骤。
10. 验证并确认数据。

## <a name="use-data-box-heavy-to-copy-data"></a>使用 Data Box Heavy 复制数据

执行以下步骤，将数据复制到 Data Box Heavy。

1. [订购 Data Box Heavy](data-box-heavy-deploy-ordered.md)。
2. 收到 Data Box Heavy 后，[设置 Data Box Heavy](data-box-heavy-deploy-set-up.md)。 需要对设备的两个节点进行布线和配置。
3. [将数据复制到 Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md)。 复制时，请确保：

    - 仅使用 Data Box Heavy 中的 *AzureFile* 文件夹来复制数据。 这是因为，数据最终会进入 Azure 文件共享，而不是进入块 Blob 或页 Blob。
    - 将文件复制到“AzureFile”文件夹中的文件夹  。 *AzureFile* 文件夹中的子文件夹会创建文件共享。 直接复制到 AzureFile 文件夹的文件都会失败，会作为块 Blob 上传  。 这是要在下一步骤中装载到 VM 上的文件共享。
    - 将数据复制到 Data Box Heavy 的两个节点。
3. 在设备上运行[准备交付](data-box-heavy-deploy-picked-up.md#prepare-to-ship)。 成功完成准备交付过程可确保将文件成功上传到 Azure。
4. [退回设备](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back)。
5. [验证数据是否已上传到 Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure)

## <a name="use-spmt-to-migrate-data"></a>使用 SPMT 迁移数据

收到 Azure 数据团队的已完成数据复制的确认后，接下来可以继续将数据迁移到 SharePoint Online。

为获得最佳性能和连接性，我们建议创建一个 Azure 虚拟机 (VM)。

1. 登录到 Azure 门户，然后选择“创建虚拟机”。[](../virtual-machines/windows/quick-create-portal.md)
2. [将 Azure 文件共享装载到 VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer)。
3. [下载 SharePoint 迁移工具](http://spmtreleasescus.blob.core.windows.net/install/default.htm)并将其安装在 Azure VM 上。
4. 启动 SharePoint 迁移工具。 单击“登录”并输入你的 Office 365 用户名和密码。 
5. 出现“数据位于何处?”提示时，请选择“文件共享”。   输入数据所在的 Azure 文件共享的路径。
6. 像平时一样遵循剩余的提示操作，包括输入目标位置。 有关详细信息，请参阅[如何使用 SharePoint 迁移工具](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)。

> [!IMPORTANT]
> - 将数据引入 SharePoint Online 的速度受多种因素的影响，不管 Azure 中是否已包含你的数据。 了解这些因素有助于规划和最大化迁移的效率。  有关详细信息，请参阅 [SharePoint Online 和 OneDrive 迁移速度](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)。
> - 将数据迁移到 SharePoint Online 时，存在失去对文件的现有权限的风险。 还可能会丢失某些元数据，例如“创建者”和“修改日期”。  

## <a name="next-steps"></a>后续步骤

[订购 Data Box Heavy](./data-box-heavy-deploy-ordered.md)
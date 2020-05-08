---
title: Microsoft Azure Data Box Gateway 用例 |Microsoft Docs
description: 介绍 Azure Data Box Gateway （一种虚拟设备存储解决方案，可用于将数据传输到 Azure）的用例
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: dde84f0973cc7e21e57574bbabe398b38581358f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562372"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Azure Data Box Gateway 的用例

Azure Data Box Gateway 是一种驻留在本地的云存储网关设备，可将图像、媒体和其他数据发送到 Azure。 此云存储网关是指虚拟机监控程序中预配的虚拟机。 可使用 NFS 和 SMB 协议将数据写入此虚拟设备，该设备随后会将数据发送到 Azure。 本文详细介绍了可以部署此设备的场景。

在以下场景中使用 Data Box Gateway：

- 要持续引入大量数据。
- 要以安全有效的方式对数据进行云存档。
- 要在使用 Data Box 完成初始批量传输后，通过网络进行增量数据传输。

后续部分将详细介绍以上每种场景。


## <a name="continuous-data-ingestion"></a>持续数据引入

Data Box Gateway 的主要优势之一就是，无论数据大小如何，都能够将数据持续引入到设备中，以复制到云端。

在将数据写入网关设备时，设备会将数据上传到 Azure 存储。 设备通过以下方式自动管理存储：在本地删除文件，同时在元数据达到特定阈值时保留元数据。 通过保留元数据的本地副本，网关设备可以在文件更新时仅上传更改。 上传到网关设备的数据应符合[数据上传注意事项](data-box-gateway-limits.md#data-upload-caveats)中的准则。

当设备中的数据变满时，它会开始限制入口速率（根据需要），以匹配数据上传到云的速率。 若要监视设备上的持续引入，请使用警报。 一旦限制开始就会引发这些警报，限制停止后就会清除这些警报。

## <a name="cloud-archival-of-data"></a>数据的云存档

若要在云中长期保留数据，请使用 Data Box Gateway。 可以使用存储的**存档**层长期保留数据。

存档层已经过优化，可以将很少访问的数据存储至少 180 天。 **存档**层的存储成本最低，但访问成本最高。 有关详细信息，请转到[存档访问层](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)。

### <a name="move-data-to-archive-tier"></a>将数据移至存档层

在开始之前，请确保正在运行 Data Box Gateway 设备。 请按照[教程：准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)中详细介绍的步骤进行操作，并继续学习下一教程，直到有一个操作设备。

- 如[通过 Data Box Gateway 传输数据](data-box-gateway-deploy-add-shares.md)中所述，使用 Data Box Gateway 设备通过常规传输过程将数据上传到 Azure。
- 上传数据后，需要将其移至存档层。 可以通过两种方式设置 blob 层： Azure PowerShell 脚本或 Azure 存储生命周期管理策略。  
    - 如果使用 Azure PowerShell，请按照这些[步骤](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier)将数据移至存档层。
    - 如果使用 Azure 生命周期管理，请按照以下步骤将数据移至存档层。
        - [注册](/azure/storage/common/storage-lifecycle-management-concepts) Blob 生命周期管理服务预览版以使用存档层。
        - 使用以下策略[在引入时存档数据](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest)。
- 将 Blob 标记为存档后，除非将它们移至热层或冷层，否则网关再也不能修改它们。 如果文件位于本地存储中，则对本地副本所做的任何更改（包括删除）都不会上传到存档层。
- 若要读取存档存储中的数据，必须通过将 Blob 层更改为热层或冷层来解除冻结。 在网关上[刷新共享](data-box-gateway-manage-shares.md#refresh-shares)不会将 Blob 解除冻结。

有关详细信息，请详细了解如何[管理 Azure Blob 存储生命周期](/azure/storage/common/storage-lifecycle-management-concepts)。

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>初始批量传输后进行增量传输

若要批量上传大量数据，然后进行增量传输，请将 Data Box 与 Data Box Gateway 结合使用。 Data Box 用于在脱机模式下进行批量传输（初始播种），Data Box Gateway 用于在网络上进行增量传输（持续馈送）。

### <a name="seed-the-data-with-data-box"></a>使用 Data Box 播种数据

按照以下步骤将数据复制到 Data Box 并上传到 Azure 存储。

1. [订购 Data Box](/azure/databox/data-box-deploy-ordered)。
2. [设置 Data Box](/azure/databox/data-box-deploy-set-up)。
3. [通过 SMB 将数据复制到 Data Box](/azure/databox/data-box-deploy-copy-data)。
4. [寄回 Data Box 并验证上传到 Azure 的数据](/azure/databox/data-box-deploy-picked-up)。
5. 向 Azure 上传完数据后，所有数据都应位于 Azure 存储容器中。 在 Data Box 的存储帐户中，转到 Blob（和文件）容器以确保复制所有数据。 记下容器名称，因为稍后将使用此名称。 例如，在以下屏幕截图中，`databox` 容器将用于增量传输。

    ![包含 Data Box 上的数据的容器](media/data-box-gateway-use-cases/data-container1.png)

此批量传输操作完成了初始播种阶段。

### <a name="ongoing-feed-with-data-box-gateway"></a>使用 Data Box Gateway 进行持续馈送

按照以下步骤使用 Data Box Gateway 进行持续引入。 

1. 在 Data Box Gateway 上创建云共享。 此共享会自动将所有数据上传到 Azure 存储帐户。 转到 Data Box Gateway 资源中的“共享”，然后单击“+ 添加共享”********。

    ![单击“+ 添加共享”](media/data-box-gateway-use-cases/add-share1.png)

2. 确保此共享映射到包含已播种数据的容器。 对于“选择 Blob 容器”，请选择“使用现有容器”并浏览到已将 Data Box 中的数据传输到的容器********。

    ![共享设置](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. 创建共享后，刷新共享。 此操作使用 Azure 中的内容刷新本地共享。

    ![刷新共享](media/data-box-gateway-use-cases/refresh-share1.png)

    同步共享时，如果在客户端上修改了文件，Data Box Gateway 将上传增量更改。

## <a name="next-steps"></a>后续步骤

- 查看 [Data Box Gateway 系统要求](data-box-gateway-system-requirements.md)。
- 了解 [Data Box Gateway 限制](data-box-gateway-limits.md)。
- 在 Azure 门户中部署 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
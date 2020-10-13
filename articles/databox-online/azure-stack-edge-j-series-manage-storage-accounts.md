---
title: Azure Stack Edge Pro GPU 存储帐户管理 |Microsoft Docs
description: 介绍如何使用 Azure 门户管理 Azure Stack Edge Pro 上的存储帐户。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d3378b08ace73b2bc9a10286abf5a48f3063ba7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743499"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>使用 Azure 门户在 Azure Stack Edge Pro 上管理边缘存储帐户

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文介绍如何在 Azure Stack Edge Pro 上管理边缘存储帐户。 可以通过 Azure 门户或通过本地 web UI 管理 Azure Stack Edge Pro。 使用 Azure 门户在设备上添加或删除边缘存储帐户。

## <a name="about-edge-storage-accounts"></a>关于边缘存储帐户

可以通过 SMB、NFS 或 REST 协议从 Azure Stack Edge Pro 设备传输数据。 若要使用 REST Api 将数据传输到 Blob 存储，需要在 Azure Stack Edge Pro 上创建边缘存储帐户。 

在 Azure Stack Edge Pro 设备上添加的边缘存储帐户将映射到 Azure 存储帐户。 写入边缘存储帐户的任何数据都会自动推送到云中。

其中详细介绍了两种类型的帐户，以及如何将这些帐户中的每个帐户流向 Azure，如下所示：

![Blob 存储帐户的关系图](media/azure-stack-edge-j-series-manage-storage-accounts/ase-blob-storage.svg)

在本文中，学习如何：

> [!div class="checklist"]
> * 添加 Edge 存储帐户
> * 删除边缘存储帐户


## <a name="add-an-edge-storage-account"></a>添加 Edge 存储帐户

若要创建 Edge 存储帐户，请执行以下过程：

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>删除边缘存储帐户

执行以下步骤可删除边缘存储帐户。

1. 在资源中，参阅 **配置 > 存储帐户** 。 从存储帐户列表中，选择要删除的存储帐户。 在顶部命令栏中，选择 " **删除存储帐户**"。

    ![请参阅存储帐户列表](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-1.png)

2. 在 " **删除存储帐户** " 边栏选项卡中，确认要删除的存储帐户，并选择 " **删除**"。

    ![确认并删除存储帐户](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-2.png)

存储帐户列表将更新以反映删除。


## <a name="add-delete-a-container"></a>添加、删除容器

你还可以添加或删除这些存储帐户的容器。

若要添加容器，请执行以下步骤：

1. 选择要管理的存储帐户。 在顶部命令栏中，选择 " **+ 添加容器**"。

    ![选择要添加容器的存储帐户](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-1.png)

2. 提供容器的名称。 此容器是在边缘存储帐户以及映射到此帐户的 Azure 存储帐户中创建的。 

    ![添加边缘容器](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-2.png)

容器列表将更新以反映新添加的容器。

![更新的容器列表](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-4.png)

你现在可以从该列表中选择一个容器，然后在顶部命令栏中选择 " **+ 删除容器** "。 

![删除容器](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>同步存储密钥

你可以在设备上同步 (本地) 存储帐户的边缘的访问密钥。 

若要同步存储帐户访问密钥，请执行以下步骤：

1. 在资源中，选择要管理的存储帐户。 在顶部命令栏中，选择 " **同步存储密钥**"。

    ![选择同步存储密钥](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-1.png)

2. 出现确认提示时，选择“是”****。

    ![选择同步存储密钥2](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](azure-stack-edge-j-series-manage-users.md)。

---
title: "使用 Azure 存储资源管理器将数据移入和移出 Azure Blob 存储 | Microsoft Docs"
description: "使用 Azure 存储资源管理器将数据移入和移出 Azure Blob 存储"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: a67f2e77d3bc7da35a03b68d7f32fd3a2a42bfcd
ms.openlocfilehash: 18c05ade1ee0c0c716307613a3c0daf1c8d853aa


---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>使用 Azure 存储资源管理器将数据移动到 Azure Blob 存储或从中移动数据
Azure 存储资源管理器是 Microsoft 免费提供的应用，可用于在 Windows、macOS 和 Linux 上处理 Azure 存储数据。 本主题介绍如何使用它从 Azure Blob 存储上载和下载数据。 该工具可以从 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)下载。

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> 如果正在使用通过 [Azure 中数据科学虚拟机](machine-learning-data-science-virtual-machines.md)提供的脚本设置的 VM，则 VM 上已安装 Azure 存储资源管理器。
> 
> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../storage/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。   
> 
> 

## <a name="prerequisites"></a>先决条件
本文档假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 上载/下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。 

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 有关创建存储帐户的说明和有关获取帐户和密钥的信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。 记下你的存储帐户的访问密钥，因为在使用 Azure Storage Explorer 工具连接到帐户时需要此密钥。
* Azure Storage Explorer 工具可以从 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)下载。 在安装过程中接受默认值。

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>使用 Azure 存储资源管理器
以下步骤介绍如何使用 Azure 存储资源管理器上载/下载数据。 

1. 启动 Microsoft Azure 存储资源管理器
2. 要打开“**登录到你的帐户**”向导，请选择“**Azure 帐户设置**”图标，然后选择“**添加帐户**”并输入凭据。 ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. 要打开“**连接到 Azure 存储**”向导，请选择“**连接到 Azure 存储**”图标。 ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. 在“**连接到 Azure 存储**”向导中，输入你的 Azure 存储帐户中的访问密钥，然后选择“**下一步**”。 ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. 在“**帐户名**”框中，输入存储帐户名称，然后选择“**下一步**”。 ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 现在应列出添加的存储帐户。 要在存储帐户中创建一个 blob 容器，请用鼠标右键单击帐户中的“**Blob 容器**”节点，选择“**创建 Blob 容器**”，然后输入一个名称。
7. 要将数据上载到容器，选择目标容器，然后单击“**上载**”按钮。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. 单击“**文件**”框右侧的 **...**，选择要从文件系统上载的一个或多个文件，然后单击“**上载**”开始上载文件。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. 要下载数据，请在相应容器中选择要下载的 blob，然后单击“**下载**”。 ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)




<!--HONumber=Nov16_HO3-->



---
title: "使用 Python 将数据移入或移出 Azure Blob 存储 | Microsoft Docs"
description: "使用 Python 将数据移入和移出 Azure Blob 存储"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 9b32c0d9c3bc19a187873eebd6ab21036ee06db8
ms.openlocfilehash: fb34986d947175ae4b4212e5bd0a1e90836ed170


---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>使用 Python 将数据移入和移出 Azure Blob 存储
本主题介绍如何使用 Python API 列出、上载和下载 Blob。 使用 Azure SDK 中提供的 Python API，可以：

* 创建容器
* 将 Blob 上载到容器中
* 下载 Blob
* 列出容器中的 Blob
* 删除 Blob

有关使用 Python API 的详细信息，请参阅[如何从 Python 使用 Blob 存储服务](../storage/storage-python-how-to-use-blob-storage.md)。

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> 如果正在使用通过 [Azure 中数据科学虚拟机](machine-learning-data-science-virtual-machines.md)提供的脚本设置的 VM，则 VM 上已安装 AzCopy。
> 
> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../storage/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

## <a name="prerequisites"></a>先决条件
本文档假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 上载/下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 有关创建存储帐户的说明和有关获取帐户和密钥的信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。

## <a name="upload-data-to-blob"></a>将数据上载到 Blob
在你希望在其中以编程方式访问 Azure 存储空间的任何 Python 代码中，将以下代码段添加到代码的顶部附近：

    from azure.storage.blob import BlobService

使用 **BlobService** 对象可以对容器和 Blob 进行操作。 以下代码使用存储帐户名称和帐户密钥创建一个 BlobService 对象。 将帐户名和帐户密钥替换为实际的帐户和密钥。

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

请使用以下方法将数据上载到 Blob：

1. put\_block\_blob\_from\_path（从指定路径上传文件内容）
2. put\_block_blob\_from\_file（从已经打开的文件/流上载内容）
3. put\_block\_blob\_from\_bytes（上载字节数组）
4. put\_block\_blob\_from\_text（使用指定的编码上载指定的文本值）

以下示例代码将本地文件上载到容器：

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

以下示例代码将本地目录中的所有文件（不包括目录）都上载到 Blob 存储:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>从 Blob 下载数据
请使用以下方法从 Blob 下载数据：

1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

这些方法用于在数据大小超过 64 MB 时执行必要的分块。

以下示例代码将容器中的 Blob 内容下载到本地文件：

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

以下示例代码从容器中下载所有 Blob。 它使用 list\_blobs 获取容器中可用的 Blob 列表并将它们下载到本地目录。

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name



<!--HONumber=Dec16_HO1-->



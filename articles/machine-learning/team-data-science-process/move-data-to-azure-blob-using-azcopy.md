---
title: 使用 AzCopy 将数据移入和移出 Azure Blob 存储 | Microsoft Docs
description: 使用 AzCopy 将数据移动到 Azure Blob 存储或从中移动数据
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 936f0a25b101e82f3c0e3ae7e1e3183b9ee38393
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>使用 AzCopy 将数据移入和移出 Azure Blob 存储
AzCopy 是一个命令行实用程序，用于将数据上传、复制到 Microsoft Azure Blob、文件和表存储以及从其中下载和复制数据。

有关安装 AzCopy 的说明和将其与 Azure 平台配合使用的其他信息，请参阅 [AzCopy 命令行实用程序入门](../../storage/common/storage-use-azcopy.md)。

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> 如果正在使用通过 [Azure 中数据科学虚拟机](virtual-machines.md)提供的脚本设置的 VM，则 VM 上已安装 AzCopy。
> 
> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

## <a name="prerequisites"></a>先决条件
本文档假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 上传/下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 有关创建存储帐户的说明和有关获取帐户和密钥的信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。

## <a name="run-azcopy-commands"></a>运行 AzCopy 命令
要运行 AzCopy 命令，请打开一个命令窗口，并导航到计算机上的 AzCopy 安装目录，该位置存放着可执行的 AzCopy.exe。 

AzCopy 命令的基本语法是：

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> 可将 AzCopy 安装位置添加到系统路径，然后从任何目录运行这些命令。 默认情况下，AzCopy 安装到 *%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy* 或 *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*。
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>将文件上传到 Azure blob
若要上传文件，请使用以下命令：

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>从 Azure blob 下载文件
若要从 Azure blob 下载文件，请使用以下命令：

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>在 Azure 容器之间传输 blob
若要在 Azure 容器之间传输 blob，请使用以下命令：

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>使用 AzCopy 的提示
> [!TIP]
> 1. **上传**文件时，*/S* 以递归方式上传文件。 如果没有此参数，则不会上传子目录中的文件。  
> 2. **下载**文件时，*/S* 以递归方式搜索容器，直到指定目录及其子目录中的所有文件，或与给定目录及其子目录中指定模式匹配的所有文件，都已下载。  
> 3. 不能指定要使用 */Source* 参数下载的**特定 blob 文件**。 若要下载特定文件，请指定要使用 */Pattern* 参数下载的 blob 文件名称。 **/S** 参数可用于使用 AzCopy 以递归方式查找文件名称模式。 如果不使用模式参数，AzCopy 将下载该目录中的所有文件。
> 
> 


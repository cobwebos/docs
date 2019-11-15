---
title: 快速入门：适用于 Python 的 Azure Blob 存储客户端库 v2.1
description: 本快速入门将在对象 (Blob) 存储中创建存储帐户和容器。 然后，使用适用于 Python 的存储客户端库 v2.1 将 blob 上传到 Azure 存储、下载 blob，然后列出容器中的 blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/11/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019
ms.openlocfilehash: 606c7d1fd012052a22afeef906bbe9df4c63a76b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825294"
---
# <a name="quickstart-azure-blob-storage-client-library-v21-for-python"></a>快速入门：适用于 Python 的 Azure Blob 存储客户端库 v2.1

本文介绍如何使用 Python 在 Azure Blob 存储的容器中上传、下载和列出块 Blob。 Blob 是简单的对象，可用于存储大量的文本或二进制数据，其中包括图像、文档、流媒体和存档数据。 Azure 存储中的 Blob 不同于文件共享、无架构表和消息队列。  有关详细信息，请参阅 [Azure 存储简介](/azure/storage/common/storage-introduction)。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

请确保已安装下述额外的必备组件：

* [Python](https://www.python.org/downloads/)

* [用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)是基本的 Python 应用程序。  

使用以下 [git](https://git-scm.com/) 命令将应用程序下载到开发环境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

若要查看 Python 程序，请在存储库的根目录中打开 *example.py* 文件。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

在应用程序中，请提供存储帐户名称和帐户密钥，以创建 `BlockBlobService` 对象。

1. 从 IDE 中的解决方案资源管理器打开 *example.py* 文件。

1. 将 `accountname` 和 `accountkey` 值替换为自己的存储帐户名和密钥：

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. 保存并关闭该文件。

## <a name="run-the-sample"></a>运行示例

示例程序会在 *Documents* 文件夹中创建一个测试文件，接着将该文件上传到 Blob 存储，列出文件中的 blob，并使用新名称下载此文件。

1. 安装依赖项：

    ```console
    pip install azure-storage-blob
    ```

1. 转到示例应用程序：

    ```console
    cd storage-blobs-python-quickstart
    ```

1. 运行示例：

    ```console
    python example.py
    ```

    此时会看到类似于以下输出的消息：
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. 继续操作前，请转到 *Documents* 文件夹，检查其中是否有这两个文件。

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. 可以打开它们，看它们是否相同。

    还可以使用工具，例如 [Azure 存储资源管理器](https://storageexplorer.com)。 它适用于查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，用于访问存储帐户信息。 

1. 查看文件后，按任意键可完成示例并删除测试文件。

## <a name="learn-about-the-sample-code"></a>了解示例代码

了解此示例的用途以后，即可打开 *example.py* 文件来查看代码。

### <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

此部分将实例化对象，新建容器，然后设置容器的权限，以便 blob 成为公共 blob。 需调用容器 `quickstartblobs`。 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

首先，请创建对用于访问和管理 Blob 存储的对象的引用。 这些对象相互关联，并且每个对象被列表中的下一个对象使用。

* 实例化 BlockBlobService 对象，该对象指向存储帐户中的 Blob 服务  。 

* 实例化 CloudBlobContainer 对象，该对象代表你正在访问的容器  。 系统使用容器来组织 Blob，就像使用计算机上的文件夹组织文件一样。

有了云 Blob 容器后，请实例化 CloudBlockBlob 对象（该对象指向你感兴趣的特定 Blob）  。 然后即可根据需要上传、下载和复制 Blob。

> [!IMPORTANT]
> 容器名称必须为小写。 有关容器名称和 Blob 名称的详细信息，请参阅 [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)（命名和引用容器、Blob 和元数据）。

### <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 最大可以为 4.7 TB，并且可以是从 Excel 电子表格到大视频文件的任何内容。 在需要写入到文件，再继续添加更多信息时，可以将追加 Blob 用于日志记录。 页 Blob 主要用于虚拟硬盘 (VHD) 文件，这些文件支持基础结构即服务虚拟机 (IaaS VM)。 块 blob 是最常用的。 本快速入门使用块 Blob。

若要将文件上传到 Blob，请通过将本地驱动器上的目录名称和文件名称联接在一起来获取完整的文件路径。 然后可以使用 `create_blob_from_path` 方法将文件上传到指定的路径。 

示例代码将创建一个本地文件，供系统用于上传和下载，并将系统要上传的此文件存储为 full_path_to_file  ，将 blob 的名称存储为 local_file_name  。 此示例将文件上传到名为 `quickstartblobs` 的容器：

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Blob 存储支持多种上传方法。 例如，若有一个内存流，则可使用 `create_blob_from_stream` 方法而不是 `create_blob_from_path` 方法。 

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

以下代码为 `list_blobs` 方法创建 `generator`。 此代码循环访问容器中的 Blob 列表，并将其名称输出到控制台。

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>下载 Blob


使用 `get_blob_to_path` 方法将 Blob 下载到本地磁盘。
以下代码下载以前上传的 Blob。 系统将“_DOWNLOADED”  追加到 Blob 名称，因此可以在本地磁盘上看到这两个文件。

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中上传的 Blob，可使用 `delete_container` 方法删除整个容器。 若要改为删除单个文件，请使用 `delete_blob` 方法。

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>用于开发包含 Blob 的 Python 应用程序的资源

若要详细了解如何使用 Blob 存储进行 Python 开发，请参阅下述额外资源：

### <a name="binaries-and-source-code"></a>二进制文件和源代码

- 在 GitHub 上查看、下载并安装 Azure 存储的 [Python 客户端库源代码](https://github.com/Azure/azure-storage-python)。

### <a name="client-library-reference-and-samples"></a>客户端库参考和示例

- 有关 Python 客户端库的详细信息，请查看[用于 Python 的 Azure 存储库](https://docs.microsoft.com/python/api/overview/azure/storage)。
- 浏览使用 Python 客户端库编写的 [Blob 存储示例](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob)。

## <a name="next-steps"></a>后续步骤
 
本快速入门介绍了如何使用 Python 在本地磁盘和 Azure Blob 存储之间传输文件。 

若要详细了解存储资源管理器和 Blob，请参阅[使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

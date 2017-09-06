---
title: "使用 Python 针对 Azure 文件存储进行开发 | Microsoft Docs"
description: "了解如何开发使用 Azure 文件存储来存储文件数据的 Python 应用程序和服务。"
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3dd14e8d3ea7d1e50f41633a7920a6d36becf789
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="develop-for-azure-file-storage-with-python"></a>使用 Python 针对 Azure 文件存储进行开发
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>关于本教程
本教程将演示使用 Python 开发应用程序或服务的基本操作，这些程序或服务可以使用 Azure 文件存储来存储文件数据。 在本教程中，我们将创建一个简单的控制台应用程序，并演示如何通过 Python 和 Azure 文件存储执行基本的操作：

* 创建 Azure 文件共享
* 创建目录
* 枚举 Azure 文件共享中的文件和目录
* 上传、下载和删除文件

> [!Note]  
> 由于 Azure 文件存储可以通过 SMB 进行访问，因此可以编写简单的应用程序，通过标准的 Python I/O 类和函数来访问 Azure 文件共享。 本文介绍如何编写使用 Azure 存储 Python SDK 的应用程序，该 SDK 使用 [Azure 文件存储 REST API](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) 与 Azure 文件存储通信。

### <a name="set-up-your-application-to-use-azure-file-storage"></a>设置应用程序以使用 Azure 文件存储
在希望在其中以编程方式访问 Azure 存储的任何 Python 源文件中，将以下代码添加到文件的顶部附近：

```python
from azure.storage.file import FileService
```

### <a name="set-up-a-connection-to-azure-file-storage"></a>设置与 Azure 文件存储的连接 
通过 `FileService` 对象，可使用共享、目录和文件。 以下代码使用存储帐户名称和帐户密钥创建一个 `FileService` 对象。 将 `<myaccount>` 和 `<mykey>` 替换为自己的帐户名和密钥。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

### <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
在以下代码示例中，如果共享不存在，可以使用 `FileService` 对象来创建它。

```python
file_service.create_share('myshare')
```

### <a name="create-a-directory"></a>创建目录
也可以将文件置于子目录中，不必将其全部置于根目录中，以便对存储进行有效的组织。 Azure 文件存储允许创建帐户允许的任意数目的目录。 以下代码会在根目录下创建名为 **sampledir** 的子目录。

```python
file_service.create_directory('myshare', 'sampledir')
```

### <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>枚举 Azure 文件共享中的文件和目录
若要列出共享中的文件和目录，请使用 **list\_directories\_and\_files** 方法。 此方法会返回一个生成器。 以下代码将共享中每个文件和目录的**名称**输出到控制台。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

### <a name="upload-a-file"></a>上传文件 
Azure 文件共享至少包含文件所在的根目录。 在本部分，你将学习如何将文件从本地存储上传到共享所在的根目录。

若要创建文件并上传数据，请使用 `create_file_from_path`、`create_file_from_stream`、`create_file_from_bytes` 或 `create_file_from_text` 方法。 这些方法属于高级方法，用于在数据大小超过 64 MB 时执行必要的分块。

`create_file_from_path` 从指定位置上传文件内容，`create_file_from_stream` 从已经打开的文件/流上传内容。 `create_file_from_bytes` 上传字节数组，`create_file_from_text` 使用指定的编码（默认为 UTF-8）上传指定的文本值。

下面的示例将 **sunset.png** 文件的内容上传到 **myfile** 文件中。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

### <a name="download-a-file"></a>下载文件
若要从文件中下载数据，请使用 `get_file_to_path`、`get_file_to_stream`、`get_file_to_bytes` 或 `get_file_to_text`。 这些方法属于高级方法，用于在数据大小超过 64 MB 时执行必要的分块。

以下示例演示如何使用 `get_file_to_path` 下载 **myfile** 文件的内容，并将其存储到 **out-sunset.png** 文件。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

### <a name="delete-a-file"></a>删除文件
最后，若要删除文件，请调用 `delete_file`。

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>后续步骤
了解如何使用 Python 操作 Azure 文件存储后，请单击以下链接了解更多信息。

* [Python 开发人员中心](/develop/python/)
* [Azure 存储服务 REST API](http://msdn.microsoft.com/library/azure/dd179355)
* [用于 Python 的 Microsoft Azure 存储 SDK](https://github.com/Azure/azure-storage-python)

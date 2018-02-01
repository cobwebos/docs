---
title: "如何通过 Ruby 使用 Blob 存储（对象存储）| Microsoft Docs"
description: "使用 Azure Blob 存储（对象存储）将非结构化数据存储在云中。"
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/18/2018
ms.author: tamram
ms.openlocfilehash: c4c6d47511acdae7afaf4a535c24c6fcc7e389b1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-use-blob-storage-from-ruby"></a>如何通过 Ruby 使用 Blob 存储
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概述
Azure Blob 存储是一种将非结构化数据作为对象/Blob 存储在云中的服务。 Blob 存储可以存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 Blob 存储也称为对象存储。

本指南演示如何使用 Blob 存储执行常见方案。 相关示例是使用 Ruby API 编写的。 涉及的任务包括**上传、列出、下载**和**删除**Blob。

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>创建 Ruby 应用程序
创建 Ruby 应用程序。 有关说明，请参阅[使用 Linux 应用服务创建 Ruby 应用](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)。


## <a name="configure-your-application-to-access-storage"></a>配置应用程序以访问存储
要使用 Azure 存储，需要下载和使用 Ruby azure 包，其中包括一组便于与存储 REST 服务进行通信的库。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 获取该程序包
1. 使用命令行接口，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。
2. 在命令窗口中键入“gem install azure-storage-blob”以安装 gem 和依赖项。

### <a name="import-the-package"></a>导入包
使用常用的文本编辑器将以下内容添加到要在其中使用存储的 Ruby 文件的顶部：

```ruby
require "azure/storage/blob"
```

## <a name="set-up-an-azure-storage-connection"></a>设置 Azure 存储连接
Azure 模块会读取环境变量 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS_KEY** 以获取连接到 Azure 存储帐户所需的信息。 如果未设置这些环境变量，则必须使用 Azure::Blob::BlobService::create 通过以下代码指定帐户信息：

```ruby
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )
```

从 Azure 门户中的经典或 Resource Manager 存储帐户中获取这些值：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要使用的存储帐户。
3. 在右侧的“设置”边栏选项卡中，单击“访问密钥”。
4. 在出现的“访问密钥”边栏选项卡中，将看到访问密钥 1 和访问密钥 2。 可以使用其中任意一个。
5. 单击复制图标以将键复制到剪贴板。

## <a name="create-a-container"></a>创建容器
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

使用 Azure::Storage::Blob::BlobService 对象可以对容器和 blob 进行操作。 若要创建容器，请使用 **create\_container()** 方法。

以下代码示例创建一个容器或输出存在的错误。

```ruby
azure_blob_service = Azure::Storage::Blob::BlobService.create_from_env
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

要将容器中的文件设为公用，可以设置容器的权限。

只需修改 <strong>create\_container()</strong> 调用即可传递 **:public\_access\_level** 选项：

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

**:public\_access\_level** 选项的有效值为：

* **blob：**指定对 Blob 的公共读取权限。 可以通过匿名请求读取此容器中的 Blob 数据，但容器数据不可用。 客户端无法通过匿名请求枚举容器中的 Blob。
* **容器：**指定对容器和 blob 数据的完整公共读取权限。 客户端可以通过匿名请求枚举容器中的 Blob，但无法枚举存储帐户中的容器。

另外，还可以通过使用 **set\_container\_acl()** 方法指定公共访问级别来修改容器的公共访问级别。

以下代码示例将更改**容器**的公共访问级别：

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>将 Blob 上传到容器中
要将内容上传到 blob，请使用 **create\_block\_blob()** 方法创建 blob，将文件或字符串用作 blob 的内容。

以下代码会将文件 **test.png** 作为名为“image-blob”的新 blob 上传到容器中。

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob
若要列出容器，请使用 **list_containers()** 方法。
若要列出容器中的 blob，请使用 **list\_blobs()** 方法。 若要列出容器中的所有 blob，必须遵从服务返回的继续令牌，并继续通过此令牌运行 list_blobs。 有关详细信息，请参阅[列出 Blob REST API](https://docs.microsoft.com/rest/api/storageservices/list-blobs)。

以下代码输出容器中的所有 blob。

```ruby
nextMarker = nil
loop do
    blobs = azure_blob_service.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

## <a name="download-blobs"></a>下载 Blob
若要下载 blob，请使用 **get\_blob()** 方法来检索内容。

以下代码示例演示了如何使用 **get\_blob()** 下载“image-blob”的内容并将其写入本地文件。

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>删除 Blob
最后，若要删除 blob，请使用 **delete\_blob()** 方法。 以下代码示例演示了如何删除 blob。

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>后续步骤
若要了解有关更复杂存储任务的信息，请访问下面的链接：

* [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)
* GitHub 上[用于 Ruby 的 Azure 存储 SDK](https://github.com/azure/azure-storage-ruby) 存储库
* [使用 AzCopy 命令行实用程序传输数据](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


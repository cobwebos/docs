---
title: "开始使用 Azure 堆栈存储开发工具"
description: "若要开始使用 Azure 堆栈存储开发工具使用的指南"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>开始使用 Azure 堆栈存储开发工具

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*


Microsoft Azure 堆栈提供了一套存储服务，包括 Azure Blob、 表和队列的存储空间。

本文提供有关如何开始使用 Azure 堆栈存储开发工具的快速指南。 可以在相应的 Azure 存储教程中找到更多详细的信息和示例代码。

存在一些已知 Azure 存储空间和 Azure 堆栈存储，包括某些特定的要求为每个平台之间的差异。 例如，有特定的客户端库和 Azure 堆栈的特定终结点后缀要求。 有关详细信息，请参阅[Azure 堆栈存储： 差异和注意事项](azure-stack-acs-differences.md)。

## <a name="azure-client-libraries"></a>Azure 客户端库
Azure 堆栈存储支持的 REST API 版本是 2015年-04-05。 它没有 Azure 存储 REST API 的最新版本的完整奇偶校验。 因此对于存储客户端库，你需要注意的是使用 REST API 2015-04-05 兼容的版本。


|客户端库|Azure 堆栈支持版本|链接|终结点规范|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget 包：<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub 版本：<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config 文件|
|Java|4.1.0|Maven 包：<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub 版本：<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|设置连接字符串|
|Node.js     |1.1.0|NPM 链接：<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(运行：`npm install azure-storage@1.1.0)`<br><br>Github 版本：<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|服务实例声明||C++|2.4.0|Nuget 包：<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub 版本：<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|设置连接字符串|
|C++|2.4.0|Nuget 包：<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub 版本：<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|设置连接字符串|
|PHP|0.15.0|GitHub 版本：<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>通过 Composer 安装 （请参阅下面的详细信息）|设置连接字符串|
|Python     |0.30.0|PIP 包：<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(运行：`pip install -v azure-storage==0.30.0)`<br><br>GitHub 版本：<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|服务实例声明|
|Ruby|0.12.1<br>预览|RubyGems 包：<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub 版本：<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|设置连接字符串|

> [!NOTE]
> PHP 详细信息<br><br>
>若要通过 Composer 安装：
>1. 创建名为的文件`composer.json`具有下面的代码的项目的根目录中：<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. 下载[composer.phar](http://getcomposer.org/composer.phar)到项目根目录。
>3. 运行：`php composer.phar install`。
>


## <a name="endpoint-declaration"></a>终结点声明
Azure 堆栈终结点包括两个部分： 一个区域和 Azure 堆栈域的名称。
在 Azure 堆栈开发工具包中，默认终结点是**local.azurestack.external**。
如果你不确定有关你的终结点，请与您的云管理员联系。

## <a name="examples"></a>示例


### <a name="net"></a>.NET

对于 Azure 堆栈的终结点后缀是在 app.config 文件指定的：

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

对于 Azure 堆栈的终结点后缀被指定的连接字符串的安装程序：

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

对于 Azure 堆栈的终结点后缀被指定的声明实例：

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

对于 Azure 堆栈的终结点后缀被指定的连接字符串的安装程序：

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

对于 Azure 堆栈的终结点后缀被指定的连接字符串的安装程序：

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

对于 Azure 堆栈的终结点后缀被指定的声明实例：

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

对于 Azure 堆栈的终结点后缀被指定的连接字符串的安装程序：

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob 存储

以下的 Azure Blob 存储教程都适用于 Azure 堆栈。 请注意 Azure 堆栈中前面所述的特定终结点后缀要求[示例](#examples)部分。

* [通过 .NET 开始使用 Azure Blob 存储](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [如何通过 Java 使用 Blob 存储](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [如何通过 Node.js 使用 Blob 存储...]/../ storage/blobs/storage-nodejs-how-to-use-blob-storage.md）
* [如何通过 C++ 使用 Blob 存储](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [如何通过 PHP 使用 Blob 存储](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [如何通过 Python 使用 Azure Blob 存储](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [如何通过 Ruby 使用 Blob 存储](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>队列存储

以下 Azure 队列存储教程都适用于 Azure 堆栈。 请注意 Azure 堆栈中前面所述的特定终结点后缀要求[示例](#examples)部分。

* [通过 .NET 开始使用 Azure 队列存储](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [如何通过 Java 使用队列存储](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [如何通过 Node.js 使用队列存储](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [如何通过 C++ 使用队列存储](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [如何通过 PHP 使用队列存储](../../storage/queues/storage-php-how-to-use-queues.md)
* [如何通过 Python 使用队列存储](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [如何通过 Ruby 使用队列存储](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>表存储

以下 Azure 表存储教程都适用于 Azure 堆栈。 请注意 Azure 堆栈中前面所述的特定终结点后缀要求[示例](#examples)部分。

* [通过 .NET 开始使用 Azure 表存储](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [如何通过 Java 使用表存储](../../cosmos-db/table-storage-how-to-use-java.md)
* [如何通过 Node.js 使用 Azure 表存储](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [如何通过 c + + 使用表存储](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [如何通过 PHP 使用表存储](../../cosmos-db/table-storage-how-to-use-php.md)
* [如何在 Python 中使用表存储](../../cosmos-db/table-storage-how-to-use-python.md)
* [如何通过 Ruby 使用表存储](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure 存储简介](../../storage/common/storage-introduction.md)
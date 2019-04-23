---
title: Microsoft Azure Data Box Blob 存储要求 | Microsoft Docs
description: 了解 Azure Data Box Blob 存储支持的 API、SDK 和客户端库版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004561"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob 存储要求

本文列出了 Azure Api、 Azure 客户端库和工具使用数据框 Blob 存储支持的版本。 Data Box Blob 存储提供具有 Azure 一致语义的 blob 管理功能。 本文还总结了 Azure 存储服务中已知的 Azure Data Box Blob 存储差异。

建议在连接到 Data Box Blob 存储之前仔细查看这些信息，然后再根据需要重新参阅。


## <a name="storage-differences"></a>存储差异

|     Feature                                             |     Azure 存储                                     |     Data Box Blob 存储 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 文件存储                                   |    支持基于云的 SMB 文件共享              |    不支持      |
|    静态数据的服务加密                  |    256 位 AES 加密                             |    256 位 AES 加密 |
|    存储帐户类型                                 |    常规用途和 Azure blob 存储帐户    |    仅常规用途 v1|
|    Blob 名称                                            |    1024 个字符（2048 个字节）                     |    880 个字符（1760 个字节）|
|    块 blob 最大大小                              |    4.75 TB（100 MB X 50,000 块）                   |    4.75 TB（100 MB x 50,000 块），用于 Azure Data Box v 1.8 及更高版本。|
|    页 blob 最大大小                               |    8 TB                                               |    1 TB                   |
|    页 blob 页面大小                                  |    512 字节                                          |    4 KB                   |

## <a name="supported-api-versions"></a>支持的 API 版本

Data Box Blob 存储支持以下版本的 Azure 存储服务 API：

Azure Data Box 1.8 及更高版本

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>支持的 Azure 客户端库

对于 Data Box Blob 存储，需要提供特定的客户端库和特定的终结点后缀。 Data Box Blob 存储终结点与最新版本的 Azure Blob 存储 REST API 不完全相同，请参阅 [Azure Data Box 1.8 及更高版本支持的版本](#supported-api-versions)。 对于存储客户端库，需要了解与 REST API 兼容的版本。

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 及更高版本

| 客户端库     |Data Box Blob 存储支持的版本     | 链接   |     终结点规范      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget 包： https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub 版本： https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config 文件                 |
|    Java                |    7.0.0                                           |    Maven 包： https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub 版本： https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    连接字符串设置         |
|    Node.js             |    2.8.3                                           |    NPM 链接： https://www.npmjs.com/package/azure-storage   (运行： `npm install azure-storage@2.7.0`)   <br>GitHub 版本： https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    服务实例声明    |
|    C++                 |    5.2.0                                           |    Nuget 包： https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub 版本： https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    连接字符串设置         |
|    PHP                 |    1.2.0                                           |    GitHub 版本：<br>通用： https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob： https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>通过 Composer 安装（请参阅下面的详细信息）。                                                                                                             |    连接字符串设置         |
|    Python              |    1.1.0                                           |    GitHub 版本：<br>通用： https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob： https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    服务实例声明    |
|    Ruby                |    1.0.1                                           |    RubyGems 包：<br>通用： https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob： https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub 版本：<br>通用： https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob： https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    连接字符串设置         |



### <a name="install-php-client-via-composer---current"></a>通过 Composer 安装 PHP 客户端 - 当前

通过 Composer 安装：（以 blob 为例）。
1. 使用以下代码在项目的根目录中创建名为 composer.json 的文件：

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. 将 `composer.phar` 下载到项目根目录。

3. 运行：php composer.phar 安装。

### <a name="endpoint-declaration"></a>终结点声明

Azure Data Box Blob 存储终结点包括两部分：区域名称和 Data Box 域。 在数据框 Blob 存储 SDK 中，默认终结点是`\<serial no. of the device>.microsoftdatabox.com`。  有关 blob 服务终结点的更多信息，请转至[通过 Data Box Blob 存储连接](data-box-deploy-copy-data-via-rest.md)。
 
## <a name="examples"></a>示例

### <a name="net"></a>.NET

对于 Data Box Blob 存储，在 `app.config` 文件中指定终结点后缀：

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

对于 Data Box Blob 存储，在连接字符串的设置中指定终结点后缀：

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

对于 Data Box Blob 存储，在声明实例中指定终结点后缀：

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

对于 Data Box Blob 存储，在连接字符串的设置中指定终结点后缀：

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

对于 Data Box Blob 存储，在连接字符串的设置中指定终结点后缀：

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

对于 Data Box Blob 存储，在声明实例中指定终结点后缀：

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

对于 Data Box Blob 存储，在连接字符串的设置中指定终结点后缀：

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)

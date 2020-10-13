---
title: Microsoft Azure Data Box Blob 存储要求 | Microsoft Docs
description: 了解 Azure Data Box Blob 存储支持的 API、SDK 和客户端库版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744080"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob 存储要求

本文列出了 Data Box Blob 存储支持的 Azure API、Azure 客户端库和工具的版本。 Data Box Blob 存储提供具有 Azure 一致语义的 blob 管理功能。 本文还总结了 Azure 存储服务中已知的 Azure Data Box Blob 存储差异。

建议在连接到 Data Box Blob 存储之前仔细查看这些信息，然后再根据需要重新参阅。


## <a name="storage-differences"></a>存储差异

|     功能                                             |     Azure 存储                                     |     Data Box Blob 存储 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 文件存储                                   |    支持基于云的 SMB 文件共享              |    不支持      |
|    静态数据的服务加密                  |    256 位 AES 加密                             |    256 位 AES 加密 |
|    存储帐户类型                                 |    常规用途和 Azure Blob 存储帐户    |    仅常规用途 v1|
|    Blob 名称                                            |    1,024 个字符（2,048 字节）                     |    880 个字符（1,760 字节）|
|    块 blob 最大大小                              |    4.75 TB（100 MB X 50,000 块）                   |    4.75 TB (100 MB x 50000 块) 用于 Azure Data Box v 3.0。|
|    页 blob 最大大小                               |    8 TB                                               |    1 TB                   |
|    页 Blob 页面大小                                  |    512 字节                                          |    4 KB                   |

## <a name="supported-api-versions"></a>支持的 API 版本

Data Box Blob 存储支持以下版本的 Azure 存储服务 Api。

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3.0 向前

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>支持的 Azure 客户端库

对于 Data Box Blob 存储，需要提供特定的客户端库和特定的终结点后缀。 Data Box Blob 存储终结点与最新版本的 Azure Blob 存储没有完全的奇偶校验 REST API;请参阅 [Azure Data Box 3.0 的支持版本](#supported-api-versions)。 对于存储客户端库，需要了解与 REST API 兼容的版本。

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3.0 向前

Data Box Blob 存储支持以下 Azure 客户端库版本。

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

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

在 Data Box Blob 存储 SDK 中，终结点后缀- `<device serial number>.microsoftdatabox.com` -标识 Data Box 域。 有关 blob 服务终结点的详细信息，请参阅 [通过 Data Box blob 存储连接](data-box-deploy-copy-data-via-rest.md)。
 
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

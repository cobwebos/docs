---
title: 使用 .NET 的 Azure 存储示例 | Microsoft Docs
description: 查看、下载和运行 Azure 存储的示例代码和应用程序 使用 .NET 存储客户端库发现 Blob、队列、表和文件的入门示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: a4c9188c8f1315a3af452cd0c1fb5cf45ab82081
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486111"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>使用 v12 .NET 客户端库的 Azure 存储示例

下表概述了示例存储库和每个示例中涉及的场景。 单击链接可查看 GitHub 中相应的示例代码。

> [!NOTE]
> 这些示例使用最新的 Azure 存储 .NET v12 库。 有关旧版 v11 代码，请参阅 GitHub 存储库中的[适用于 .NET 的 Azure Blob 存储示例](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started)。

## <a name="blob-samples"></a>Blob 示例

### <a name="authentication"></a>身份验证

:::row:::
   :::column span="":::
      [使用连接字符串进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [使用共享密钥凭据进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 Azure 标识进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [使用 Active Directory 令牌进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [匿名访问公共 Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>批处理

:::row:::
   :::column span="":::
      [在一个请求中删除多个 Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [在一个请求中设置多个 Blob 访问层](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [批处理请求中的精细控制](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [捕获失败子操作的错误](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [将文件上传到 Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [将 Blob 下载到文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [下载映像](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [列出容器中的所有 Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>故障排除
:::row:::
   :::column span="2":::
      [使用容器客户端触发可恢复的错误](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 示例

### <a name="authentication"></a>身份验证

:::row:::
   :::column span="":::
      [匿名访问公共文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [使用共享密钥凭据进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用共享访问签名 (SAS) 进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [使用 Active Directory 令牌进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>文件系统
:::row:::
   :::column span="":::
      [使用文件系统客户端创建文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [获取文件和目录的属性](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [重命名文件和目录](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>目录

:::row:::
   :::column span="":::
      [创建目录](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [使用目录客户端创建文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [列出目录](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [遍历文件和目录](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>文件
:::row:::
   :::column span="":::
      [上传文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [通过追加到文件进行上传](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [下载文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [设置和获取文件访问控制列表](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [设置和获取文件的权限](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>故障排除

:::row:::
   :::column span="2":::
      [触发可恢复的错误](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure 文件存储示例

### <a name="authentication"></a>身份验证

:::row:::
   :::column span="":::
      [使用连接字符串进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [使用共享密钥凭据进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [使用共享访问签名 (SAS) 进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>文件共享

:::row:::
   :::column span="":::
      [创建共享和上传文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [下载文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [遍历文件和目录](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>故障排除

:::row:::
   :::column span="2":::
      [使用共享客户端触发可恢复的错误](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>队列示例

### <a name="authentication"></a>身份验证

:::row:::
   :::column span="":::
      [使用 Azure Active Directory 进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [使用连接字符串进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用共享密钥凭据进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [使用共享访问签名 (SAS) 进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [使用 Active Directory 令牌进行身份验证](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>队列

:::row:::
   :::column span="2":::
      [创建队列和添加消息](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>消息

:::row:::
   :::column span="":::
      [接收和处理消息](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [扫视消息](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [接收消息和更新可见性超时](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>故障排除 
:::row:::
   :::column span="2":::
      [使用队列客户端触发可恢复的错误](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>表示例 (v11)

:::row:::
   :::column span="":::
      [创建表](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [删除实体/表](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [插入/合并/替换实体](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [查询实体](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [查询表](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [表 ACL/属性](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [更新实体](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure 代码示例库

若要查看完整的 .NET 示例库，请转到：

* [Azure Blob 代码示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Azure Data Lake 代码示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Azure 文件存储代码示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Azure 队列代码示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

可以浏览并克隆每个库的 GitHub 存储库。

## <a name="getting-started-guides"></a>入门指南

如果要查找有关如何安装和开始使用 Azure 存储客户端库的说明，请查看以下指南。

* [.NET 中 Azure Blob 服务入门](../blobs/storage-quickstart-blobs-dotnet.md)
* [Getting Started with Azure Queue Service in .NET](../queues/storage-quickstart-queues-dotnet.md)（.NET 中 Azure 队列服务入门）
* [.NET 中 Azure 表服务入门](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Getting Started with Azure File Service in .NET](../files/storage-dotnet-how-to-use-files.md)（.NET 中 Azure 文件服务入门）

## <a name="next-steps"></a>后续步骤

了解有关其他语言的示例的信息：

* Java:[使用 Java 的 Azure 存储示例](storage-samples-java.md)
* Python:[使用 Python 的 Azure 存储示例](storage-samples-python.md)
* JavaScript/Node.js：[使用 JavaScript 的 Azure 存储示例](storage-samples-javascript.md)
* 所有其他语言：[Azure 存储示例](storage-samples.md)

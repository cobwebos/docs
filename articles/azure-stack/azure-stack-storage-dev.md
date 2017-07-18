---
title: Get started with Azure Stack Storage development tools
description: Guidance to get started with using Azure Stack Storage development tools
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 7/18/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: f8c2838c63f00229816e4e05e9c3ea05fbf4e7f1
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017

---

# <a name="get-started-with-azure-stack-storage-development-tools"></a>Get started with Azure Stack Storage development tools 

Microsoft Azure Stack provides a set of storage services, including Azure Blob, Table, and Queue storage.

This article provides quick guidance on how to start using Azure Stack Storage development tools. You can find more detailed information and sample code in the corresponding Azure Storage tutorials.

There are known differences between Azure Storage and Azure Stack Storage, including some specific requirements for each platform. For example, there are specific client libraries and specific endpoint suffix requirements for Azure Stack. For more information, see [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure client libraries
The supported REST API version for Azure Stack Storage is 2015-04-05. It doesn’t have full parity with the latest version of the Azure Storage REST API. So for the storage client libraries, you need to be aware of the version that is compatible with REST API 2015-04-05.


|Client library|Azure Stack supported version|Link|Endpoint specification|
|---------|---------|---------|---------|
|.NET     |6.2.0|[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config file|
|Node.js     |1.1.0|[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Service instance declaration|
|Python     |0.30.0|[https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Service instance declaration|

## <a name="endpoint-declaration"></a>Endpoint declaration
An Azure Stack endpoint includes two parts: the name of a region and the Azure Stack domain.
In the Azure Stack Development Kit, the default endpoint is **local.azurestack.external**.
Contact your cloud administrator if you’re not sure about your endpoint.

## <a name="examples"></a>Examples


### <a name="net"></a>.NET

For Azure Stack, the endpoint suffix is specified in the app.config file:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="nodejs"></a>Node.js

For Azure Stack, the endpoint suffix is specified in the declaration instance:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="python"></a>Python

For Azure Stack, the endpoint suffix is specified in the declaration instance:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

## <a name="blob-storage"></a>Blob storage

The following Azure Blob storage tutorials are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Blob storage using .NET](../storage/storage-dotnet-how-to-use-blobs.md)
* [How to use Blob storage from Node.js](../storage/storage-nodejs-how-to-use-blob-storage.md)
* [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue storage

The following Azure Queue storage tutorials are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Queue storage using .NET](../storage/storage-dotnet-how-to-use-queues.md)
* [How to use Queue storage from Node.js](../storage/storage-nodejs-how-to-use-queues.md)
* [How to use Queue storage from Python](../storage/storage-python-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Table storage

The following Azure Table storage tutorials are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Table storage using .NET](../storage/storage-dotnet-how-to-use-tables.md)
* [How to use Azure Table storage from Node.js](../storage/storage-nodejs-how-to-use-table-storage.md)
* [How to use Table storage in Python](../storage/storage-python-how-to-use-table-storage.md)

## <a name="next-steps"></a>Next steps

* [Introduction to Microsoft Azure Storage](../storage/storage-introduction.md)

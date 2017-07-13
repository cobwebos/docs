---
title: Get started with Azure Stack Storage development tools
description: Guidance to get started with using Azure Stack Storage development tools
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 7/10/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: f8c2838c63f00229816e4e05e9c3ea05fbf4e7f1
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017

---

# Get started with Azure Stack Storage development tools
<a id="get-started-with-azure-stack-storage-development-tools" class="xliff"></a> 

Microsoft Azure Stack provides a set of storage services, including Azure Blob, Table, and Queue storage.

This article provides quick guidance on how to start using Azure Stack Storage development tools. You can find more detailed information and sample code in the corresponding Azure Storage tutorials.

There are known differences between Azure Storage and Azure Stack Storage, including some specific requirements for each platform. For example, there are specific client libraries and specific endpoint suffix requirements for Azure Stack. For more information, see [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md).

## Azure client libraries
<a id="azure-client-libraries" class="xliff"></a>
The supported REST API version for Azure Stack Storage is 2015-04-05. It doesn’t have full parity with the latest version of the Azure Storage REST API. So for the storage client libraries, you need to be aware of the version that is compatible with REST API 2015-04-05.


|Client library|Azure Stack supported version|Link|Endpoint specification|
|---------|---------|---------|---------|
|.NET     |6.2.0|[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config file|
|Node.js     |1.1.0|[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Service instance declaration|
|Python     |0.30.0|[https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Service instance declaration|

## Endpoint declaration
<a id="endpoint-declaration" class="xliff"></a>
An Azure Stack endpoint includes two parts: the name of a region and the Azure Stack domain.
In the Azure Stack Development Kit, the default endpoint is **local.azurestack.external**.
Contact your cloud administrator if you’re not sure about your endpoint.

## Examples
<a id="examples" class="xliff"></a>


### .NET
<a id="net" class="xliff"></a>

For Azure Stack, the endpoint suffix is specified in the app.config file:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### Node.js
<a id="nodejs" class="xliff"></a>

For Azure Stack, the endpoint suffix is specified in the declaration instance:

```
var blobSvc = azure.createBlobService(‘'myaccount', ‘mykey’,
‘myaccount.blob.local.azurestack.external’);
```

### Python
<a id="python" class="xliff"></a>

For Azure Stack, the endpoint suffix is specified in the declaration instance:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

## Blob storage
<a id="blob-storage" class="xliff"></a>

The following Azure Blob storage tutorials are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Blob storage using .NET](../storage/storage-dotnet-how-to-use-blobs.md)
* [How to use Blob storage from Node.js](../storage/storage-nodejs-how-to-use-blob-storage.md)
* [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md)

## Queue storage
<a id="queue-storage" class="xliff"></a>

The following Azure Queue storage tutorials are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Queue storage using .NET](../storage/storage-dotnet-how-to-use-queues.md)
* [How to use Queue storage from Node.js](../storage/storage-nodejs-how-to-use-queues.md)
* [How to use Queue storage from Python](../storage/storage-python-how-to-use-queue-storage.md)


## Table storage
<a id="table-storage" class="xliff"></a>

The following Azure Table storage tutorials are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Table storage using .NET](../storage/storage-dotnet-how-to-use-tables.md)
* [How to use Azure Table storage from Node.js](../storage/storage-nodejs-how-to-use-table-storage.md)
* [How to use Table storage in Python](../storage/storage-python-how-to-use-table-storage.md)

## Next steps
<a id="next-steps" class="xliff"></a>

* [Introduction to Microsoft Azure Storage](../storage/storage-introduction.md)

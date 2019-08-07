---
title: 通过 .NET 获取存储帐户类型和 SKU 名称-Azure 存储
description: 了解如何使用 .NET 客户端库获取 Azure 存储帐户类型和 SKU 名称。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829198"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>通过 .NET 获取存储帐户类型和 SKU 名称

本文介绍如何使用[用于 .net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage/client)获取 Blob 的 azure 存储帐户类型和 SKU 名称。

从版本2018-03-28 开始, 服务版本中提供了帐户信息。

## <a name="about-account-type-and-sku-name"></a>关于帐户类型和 SKU 名称

**帐户类型**:有效的帐户类型`BlobStorage`包括`BlockBlobStorage`、 `FileStorage`、 `Storage`、和`StorageV2`。 [Azure 存储帐户概述](storage-account-overview.md)包含了详细信息, 包括各种存储帐户的说明。

**SKU 名称**:有效的 SKU 名称`Premium_LRS`包括`Premium_ZRS`、 `Standard_GRS` `Standard_GZRS` 、、`Standard_LRS`、 、`Standard_RAGRS`、和`Standard_ZRS`。 `Standard_RAGZRS` SKU 名称区分大小写, 并且是[SkuName 类](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)中的字符串字段。

## <a name="retrieve-account-information"></a>检索帐户信息

若要获取与 blob 关联的存储帐户类型和 SKU 名称, 请调用[GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet)或[GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)方法。

下面的代码示例检索并显示只读帐户属性。

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>后续步骤

了解可通过[Azure 门户](https://portal.azure.com)和 Azure REST API 在存储帐户上执行的其他操作。

- [管理存储帐户](storage-account-manage.md)
- [升级存储帐户](storage-account-upgrade.md)
- [获取帐户信息操作 (REST)](/rest/api/storageservices/get-account-information)

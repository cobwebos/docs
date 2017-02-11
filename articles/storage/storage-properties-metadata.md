---
title: "在 Azure 存储中设置和检索对象的属性和元数据 | Microsoft Docs"
description: "在 Azure 存储空间中存储对象的自定义元数据，并设置和检索系统属性。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e89921509bb273d6d97f829d4867eded20c82bc


---
# <a name="set-and-retrieve-properties-and-metadata"></a>设置和检索属性与元数据
## <a name="overview"></a>概述
Azure 存储中的对象支持系统属性和用户定义的元数据，除了该数据以外，它们还包含：

* **系统属性。** 系统属性存在于每个存储资源上。 其中一些属性是可以读取或设置的，而另一些属性是只读的。 事实上，有些系统属性与某些标准 HTTP 标头对应。 Azure 存储客户端库为您维护这些内容。
* **用户定义的元数据。** 用户定义的元数据是在给定资源上以名称/值对的形式指定的元数据。 您可以使用元数据来存储存储资源其他值；这些值仅用于您个人目的，不会影响资源的行为方式。

检索资源的属性和元数据值的过程分为两步。 必须先调用 **FetchAttributes** 方法显式获取这些值，才能读取它们。

> [!IMPORTANT]
> 不会填充存储资源的属性和元数据值，除非调用 **FetchAttributes** 方法之一。
>
>

## <a name="setting-and-retrieving-properties"></a>设置和检索属性
若要检索属性值，请对 Blob 或容器调用 **FetchAttributes** 方法以填充属性，然后读取值。

若要在对象上设置属性，请指定属性值，然后调用 **SetProperties** 方法。

以下代码示例创建容器并将它的一些属性值写入到控制台窗口：

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
container.FetchAttributes();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>设置和检索元数据
你可以在 Blob 或容器资源上指定元数据作为一个或多个名称/值对。 若要设置元数据，请将名称/值对添加到资源上的 **Metadata** 集合，然后调用 **SetMetadata** 方法以将值保存到服务。

> [!NOTE]
> 元数据的名称必须符合 C# 标识符命名约定。
>
>

以下代码示例在容器上设置元数据。 一个值是使用集合的 **Add** 方法设置的。 另一个值是使用隐式键/值语法设置的。 这两种方法都有效。

```csharp
public static void AddContainerMetadata(CloudBlobContainer container)
{
    //Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    //Set the container's metadata.
    container.SetMetadata();
}
```

若要检索元数据，请对 blob 或容器调用 **FetchAttributes** 方法以填充 **Metadata** 集合，然后读取值，如下面的示例所示。

```csharp
public static void ListContainerMetadata(CloudBlobContainer container)
{
    //Fetch container attributes in order to populate the container's properties and metadata.
    container.FetchAttributes();

    //Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="see-also"></a>另请参阅
* [适用于 .NET 的 Azure 存储客户端库参考](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [适用于 .NET 包的 Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/)




<!--HONumber=Nov16_HO3-->



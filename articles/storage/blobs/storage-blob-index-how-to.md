---
title: 利用 Blob 索引管理和查找 Azure Blob 存储上的数据
description: 请参阅如何使用 Blob 索引标记来分类、管理和查询以发现 Blob 对象的示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 9ba151aa1ddc7f4b14d5f4ec7f1990e2fd760602
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121229"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>利用 Blob 索引标记（预览版）来管理和查找 Azure Blob 存储上的数据

Blob 索引标记使用键/值标记特性对存储帐户中的数据进行分类。 这些标记会自动编制索引，并作为可查询多维索引公开，以方便地查找数据。 本文介绍如何使用 blob 索引标记设置、获取和查找数据。

若要了解有关 Blob 索引的详细信息，请参阅[在 Azure Blob 存储中管理和查找数据（预览版）](storage-manage-find-blobs.md)。

> [!NOTE]
> Blob 索引处于公共预览阶段，在**华北**和**法国南部**区域提供。 若要了解有关此功能以及已知问题和限制的详细信息，请参阅[在 Azure Blob 存储中管理和查找数据（预览版）](storage-manage-find-blobs.md)。

## <a name="prerequisites"></a>先决条件
# <a name="portal"></a>[门户](#tab/azure-portal)
- 订阅已注册并批准访问 Blob 索引预览
- 访问[Azure 门户](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
由于 Blob 索引处于公共预览中，因此将在预览版 NuGet 源中发布 .NET 存储包。 此库将在现在和成为官方时进行更改。 

1. 在 Visual Studio 中，将 URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` 添加到 NuGet 包源。 

   若要了解如何操作，请参阅[包源](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)。

2. 在 NuGet 包管理器中，找到 12.5.0**包，并将版本** **20200422.2**安装到你的项目。 你还可以运行命令```Install-Package Azure.Storage.Blobs -Version12.5.0-dev.20200422.2```

   若要了解如何操作，请参阅[查找和安装包](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

3. 将以下 using 语句添加到代码文件的顶部。
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>上传带有索引标记的新 blob
# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在[Azure 门户](https://portal.azure.com/)中，选择存储帐户 

2. 导航到 " **Blob 服务**" 下的 "**容器**" 选项，选择容器

3. 选择“上传”按钮打开“上传”边栏选项卡，并浏览本地文件系统，找到要作为块 Blob 上传的文件。 

4. 展开 "**高级**" 下拉列表并中转到 " **Blob 索引标记**" 部分

5. 输入要应用于数据的键/值 blob 索引标记

6. 选择 "**上传**" 按钮上传 blob

![将数据上载到 blob 索引标记](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
下面的示例演示如何创建在创建过程中设置了标记的追加 blob。
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>获取、设置和更新 blob 索引标记
# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在[Azure 门户](https://portal.azure.com/)中，选择存储帐户 

2. 导航到 " **Blob 服务**" 下的 "**容器**" 选项，选择容器

3. 从所选容器中的 blob 列表中选择所需的 blob

4. "Blob 概述" 选项卡将显示 blob 的属性，包括任何**Blob 索引标记**

5. 可以获取、设置、修改或删除 blob 的任何键/值索引标记

6. 选择 "**保存**" 按钮，确认对 blob 的任何更新

![获取、设置、更新和删除对象上的 blob 索引标记](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>筛选和查找包含 blob 索引标记的数据

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，"Blob 索引标记" 筛选器会自动应用 `@container` 参数以限定所选容器的范围。 如果希望在整个存储帐户中筛选和查找标记的数据，请使用我们的 REST API、Sdk 或工具。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择存储帐户。 

2. 导航到 " **Blob 服务**" 下的 "**容器**" 选项，选择容器

3. 选择要在所选容器中进行筛选的 " **Blob 索引标记" 筛选器**按钮

4. 输入 Blob 索引标记键和标记值

5. 选择 " **Blob 索引标记" 筛选器**按钮以添加其他标记筛选器（最多10个）

![使用 blob 索引标记筛选和查找标记对象](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>生命周期管理和 blob 索引标记筛选器

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择存储帐户。 

2. 导航到 " **Blob 服务**" 下的**生命周期管理**选项

3. 选择 "*添加规则*"，然后填写操作设置窗体字段

4. 选择 "筛选器集"，为前缀匹配添加可选筛选器，并选择 "Blob 索引匹配项" ![ 添加 blob 索引标记筛选器](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. 选择 "查看" " **+ 添加**" 以查看 ![ 带有 blob 索引标记的规则设置生命周期管理规则筛选器示例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. 选择 "**添加**" 以将新规则应用到生命周期管理策略

# <a name="net"></a>[.NET](#tab/net)
[生命周期管理](storage-lifecycle-management-concepts.md)策略在控制平面级别应用于每个存储帐户。 对于 .NET，安装[Microsoft Azure 管理存储库版本 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/)或更高版本，以利用生命周期管理规则中的 Blob 索引匹配筛选器。

---

## <a name="next-steps"></a>后续步骤

了解有关 Blob 索引的详细信息。 请参阅[在 Azure Blob 存储中管理和查找数据（预览版）](storage-manage-find-blobs.md )

了解有关生命周期管理的详细信息。 请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)

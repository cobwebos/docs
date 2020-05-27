---
title: 利用 Blob 索引管理和查找 Azure Blob 存储上的数据
description: 请参阅相关示例，了解如何使用 Blob 索引标记进行发现 Blob 对象所需的分类、管理和查询。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: cc82b6578b06323d8cf9a09644d50043dba8e554
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774332"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>利用 Blob 索引标记（预览版）管理和查找 Azure Blob 存储上的数据

Blob 索引标记使用键/值标记特性对存储帐户中的数据进行分类。 这些标记会自动编制索引，并作为可查询的多维索引公开，以便你轻松查找数据。 本文介绍了如何使用 blob 索引标记来设置、获取和查找数据。

若要详细了解 Blob 索引，请参阅[通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)。

> [!NOTE]
> Blob 索引为公共预览版，在**法国中部**和**法国南部**区域可用。 若要详细了解此功能以及已知问题和限制，请参阅[通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)。

## <a name="prerequisites"></a>先决条件
# <a name="portal"></a>[门户](#tab/azure-portal)
- 已注册订阅并已批准它访问 Blob 索引预览版
- 对 [Azure 门户](https://portal.azure.com/)的访问权限

# <a name="net"></a>[.NET](#tab/net)
由于 Blob 索引为公共预览版，因此将在预览版 NuGet 源中发布 .NET 存储包。 从现在到正式发布之前的这段时间内，此库可能会更改。 

1. 在 Visual Studio 中，将 URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` 添加到 NuGet 包源。 

   若要了解如何操作，请参阅[包源](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)。

2. 在 NuGet 包管理器中查找 **Azure.Storage.Blobs** 包，并将 **12.5.0-dev.20200422.2** 版本安装到你的项目。 还可以运行 ```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2``` 命令

   若要了解如何操作，请参阅[查找并安装包](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

3. 将下列 using 语句添加到你的代码文件的顶部。
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

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户 

2. 导航到“Blob 服务”下的“容器”选项，选择你的容器

3. 选择“上传”按钮打开“上传”边栏选项卡，并浏览本地文件系统，找到要作为块 Blob 上传的文件。

4. 展开“高级”下拉列表，并转到“Blob 索引标记”部分

5. 输入要应用于数据的键/值 blob 索引标记

6. 选择“上传”按钮以上传 Blob

![上传带有 blob 索引标记的数据](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
下面的示例展示了如何使用在创建过程中设置的标记创建一个追加 blob。
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

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户 

2. 导航到“Blob 服务”下的“容器”选项，选择你的容器

3. 从所选容器的 blob 列表中选择所需的 blob

4. Blob 概览选项卡会显示 blob 的属性，包括任何 **Blob 索引标记**

5. 可以获取、设置、修改或删除 blob 的任何键/值索引标记

6. 选择“保存”按钮以确认对 blob 所做的任何更新

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

## <a name="filter-and-find-data-with-blob-index-tags"></a>筛选和查找带有 blob 索引标记的数据

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，“Blob 索引标记”筛选器会自动应用 `@container` 参数，以限制所选容器的范围。 如果希望在整个存储帐户中筛选和查找带标记的数据，请使用我们的 REST API、SDK 或工具。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户。 

2. 导航到“Blob 服务”下的“容器”选项，选择你的容器

3. 选择“Blob 索引标记筛选器”按钮，在所选容器内进行筛选

4. 输入 Blob 索引标记键和标记值

5. 选择“Blob 索引标记筛选器”按钮，以便添加更多标记筛选器（最多 10 个）

![使用 blob 索引标记筛选和查找带标记的对象](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

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

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>使用 blob 索引标记筛选器进行生命周期管理

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户。 

2. 导航到“Blob 服务”下的“生命周期管理”选项 

3. 选择“添加规则”，然后填写操作集窗体字段

4. 选择“筛选器集”，为“前缀匹配”和“Blob 索引匹配”添加可选筛选器 ![添加 blob 索引标记筛选器以进行生命周期管理](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. 选择“查看 + 添加”来查看规则设置 ![使用 blob 索引标记筛选器进行生命周期管理的示例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. 选择“添加”，将新规则应用于生命周期管理策略

# <a name="net"></a>[.NET](#tab/net)
[生命周期管理](storage-lifecycle-management-concepts.md)策略在控制平面级别应用于每个存储帐户。 对于 .NET，请安装 [Microsoft Azure 管理存储库版本 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 或更高版本，以在生命周期管理规则中利用 Blob 索引匹配筛选器。

---

## <a name="next-steps"></a>后续步骤

详细了解 Blob 索引。 请参阅[通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md )

详细了解生命周期管理。 请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)

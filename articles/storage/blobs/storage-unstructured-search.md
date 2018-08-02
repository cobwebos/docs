---
title: 在 Azure 云存储中搜索非结构化数据
description: 使用 Azure 搜索来搜索非结构化数据。
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: eba2ef280e60693cfd4402348fe61b122cdccdf6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399850"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>在云存储中搜索非结构化数据

本教程介绍如何使用在 Azure blob 中存储的数据通过 [Azure 搜索](../../search/search-what-is-azure-search.md)来搜索非结构化数据。 非结构化数据或者不按预定义的方式组织，或者不具备数据模型。 例如，.txt 文件。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建资源组
> * 创建存储帐户
> * 创建容器
> * 将数据上传到容器
> * 在门户中创建搜索服务
> * 使用搜索服务搜索容器

## <a name="download-the-sample"></a>下载示例

我们已准备好示例数据集。 **下载 [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** 并解压缩到所在文件夹中。

此示例包含从[clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) 获取的文本文件。 可以使用它们作为示例文本文件，从而使用 Azure 进行搜索。

## <a name="log-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](http://portal.azure.com)。

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户提供唯一的位置来存储和访问 Azure 存储数据对象。

目前，有两种类型的存储帐户，分别是 Blob和常规用途存储帐户。 本教程介绍如何创建常规用途存储帐户。

如果不熟悉创建常规用途存储帐户的过程，下面提供了如何创建的说明：

1. 从左侧菜单选择“存储帐户”，然后选择“添加”。

2. 为存储帐户输入唯一的名称。 

3. 为“部署模型”选择“资源管理器”，并从“帐户类型”下拉列表选择“常规用途”。

4. 从“复制”下拉列表选择“本地冗余存储 (LRS)”。

5. 在“资源组”下，选择“新建”并输入一个唯一的名称。

6. 选择相应的订阅。

7. 选择一个位置，然后选择“创建”。

  ![非结构化搜索](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>创建容器

这些容器类似于文件夹，用于存储 blob。

在本教程中，可以使用单个容器来存储从 clinicaltrials.gov 获取的文本文件。

1. 导航到 Azure 门户中的存储帐户。

2. 在“Blob 服务”下选择“浏览 blob”。

3. 添加新容器。

4. 将容器命名为“data”并选择“容器”作为公共访问级别。

5. 选择“确定”创建容器。 

  ![非结构化搜索](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>上传示例数据

现在，你已拥有一个容器，可以将示例数据上传到该容器中。

1. 选择容器，然后选择“上传”。

2. 选择“文件”字段旁的蓝色文件夹图标，浏览到提取示例数据的本地文件夹。

3. 选择已提取的所有文件，然后选择“打开”

4. 选择“上传”，开始上传过程。

  ![非结构化搜索](media/storage-unstructured-search/upload.png)

上传过程可能需要一些时间。

上传完成后，导航回数据容器，确认文本文件已上传。

  ![非结构化搜索](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>创建搜索服务

Azure 搜索是一种搜索即服务云解决方案，它提供开发人员 API 和工具，以便基于 Web、移动和企业应用程序中的数据添加丰富的搜索体验。

如果不熟悉创建搜索服务的过程，下面提供了如何创建的说明：

1. 导航到 Azure 门户中的存储帐户。

2. 向下滚动并单击“BLOB 服务”下的“添加 Azure 搜索”。

3. 在“导入数据”下，选择“选取服务”。

4. 选择“单击此处创建一个新的搜索服务”。

5. 在“新建搜索服务”**的**“URL”字段中，输入搜索服务的唯一名称。

6. 在“资源组”下，选择“使用现有资源组”并选择前面创建的资源组。

7. 为“定价层”选择“免费”层，然后单击“选择”。

8. 选择“创建”，创建搜索服务。

  ![非结构化搜索](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>将搜索服务连接到容器

现在，你已拥有一个搜索服务，可以将其附加到 blob 存储。 本部分将分步介绍选择数据源、创建索引，以及创建索引器的过程。

1. 导航到存储帐户。

2. 选择“BLOB 服务”下的“添加 Azure 搜索”。

3. 在“导入数据”内选择“搜索服务”，然后单击在上一节中创建的搜索服务。 此操作将打开“新建数据源”。

### <a name="new-data-source"></a>新建数据源

  数据源指定要编制索引的数据以及如何访问数据。 一个数据源可供同一搜索服务多次使用。

1. 输入数据源的名称。 在“要提取的数据”下，选择“内容和元数据”。 数据源指定要对 blob 的哪些部分编制索引。
    
    a. 在自己将来的应用场景中，还可以选择“仅存储元数据”。 如果希望将编制索引的数据限制为标准 blob 属性或用户定义的属性，可选择此选项。
    
    b. 另外，还可以选择“所有元数据”来获取两个标准 blob 属性和所有内容类型特定的元数据。 

2. 由于所用的 blob 是文本文件，因此请将“分析模式”设置为“文本”。
    
    a. 在将来的应用场景中，用户可能想要选择[其他分析模式](../../search/search-howto-indexing-azure-blob-storage.md)，具体取决于 blob 的内容。

  ![非结构化搜索](media/storage-unstructured-search/datasources.png)

3. 选择“存储容器”，列出可用的存储帐户。

4. 选择存储帐户，然后选择之前创建的容器。

5. 单击“选择”返回到“新数据源”，然后选择“确定”以继续。

  ![非结构化搜索](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>配置索引

  索引是来自可搜索的数据源的字段集合。 索引可让搜索服务知道应该以何种方式搜索数据。

1. 在“导入数据”中，选择“自定义目标索引”。
 
2. 在“索引名称”字段中输入索引的名称。

3. 选中“metadata_storage_name”下的“可检索”属性的复选框。

  ![非结构化搜索](media/storage-unstructured-search/valuestoselect.png)

4. 单击“确定”，将会弹出“创建索引器”对话框。

编制索引的参数和为这些参数提供的属性都非常重要。 参数指定要存储*什么*数据，属性指定*如何*存储这些数据。

“字段名称”列包含参数。 下表提供了可用属性及其说明的列表。

### <a name="field-attributes"></a>字段属性
| 属性 | Description |
| --- | --- |
| *键* |为每个文档提供唯一 ID 以便查找文档的字符串。 每个索引必须有一个 key。 只有一个字段可以是 key，并且此字段类型必须设置为 Edm.String。 |
| *Retrievable* |指定是否可以在搜索结果中返回字段。 |
| *Filterable* |允许在筛选查询中使用字段。 |
| *Sortable* |允许查询使用此字段对搜索结果排序。 |
| *Facetable* |允许在分面导航结构中使用字段进行用户自主筛选。 通常，包含重复值的字段更适合分面导航，这些重复值可用于将多个文档（例如，同属一个品牌或服务类别的多个文档）组合在一起。 |
| *Searchable* |将字段标记为可全文搜索。 |


### <a name="create-an-indexer"></a>创建索引器
    
  索引器将数据源与搜索索引关联，并提供对数据重新编制索引的计划。

1. 在“名称”字段中输入一个名称，然后选择“确定”。

  ![非结构化搜索](media/storage-unstructured-search/exindexer.png)

2. 将返回到“导入数据”，选择“确定”完成关联过程。

现在已成功将 blob 关联到搜索服务。 几分钟后，门户中才能显示索引已填充。 但是，搜索服务会立即开始编制索引，因此，可以即刻开始搜索。

## <a name="search-your-text-files"></a>搜索文本文件

若要搜索文件，请打开新创建的搜索服务的索引内的搜索资源管理器。

以下步骤介绍在何处可以找到搜索资源管理器，并提供一些示例查询：

1. 导航到所有资源，并查找新创建的搜索服务。

  ![非结构化搜索](media/storage-unstructured-search/exampleurl.png)

3. 选择索引并打开它。 

  ![非结构化搜索](media/storage-unstructured-search/overview.png)

4. 选择“搜索资源管理器”打开搜索资源管理器，可以在其中对数据进行实时查询。

  ![非结构化搜索](media/storage-unstructured-search/indexespane.png)

5. 在查询字符串字段为空时选择“搜索”。 空查询返回 blob 中的*所有*数据。

  ![非结构化搜索](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>全文搜索 

在“查询字符串”字段中输入“Myopia”，然后选择“搜索”。 启动文件内容的搜索，并返回它们的子集，其中包含单词“Myopia”。

  ![非结构化搜索](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>系统属性搜索

还可以使用 `$select` 参数创建按系统属性搜索的查询。 在查询字符串中输入 `$select=metadata_storage_name` 并按 Enter 键，将会仅返回该特定字段。
    
查询字符串直接修改 URL，因此不允许有空格。 若要搜索多个字段，请使用逗号，如：`$select=metadata_storage_name,metadata_storage_path`
    
`$select` 参数只能用于在定义索引时标记为“可检索”的字段。

  ![非结构化搜索](media/storage-unstructured-search/metadatasearchunstructured.png) 

现在已经完成了本教程，并具有一个可搜索的非结构化数据集。

## <a name="next-steps"></a>后续步骤

通过本教程您已经了解如何使用 Azure 搜索来搜索非结构化数据，例如，如何执行以下操作：

> [!div class="checklist"]
> * 创建资源组
> * 创建存储帐户
> * 创建容器
> * 将数据上传到容器
> * 创建搜索服务
> * 使用搜索服务搜索容器

请访问此链接，详细了解如何使用 Azure 搜索对文档编制索引。

> [!div class="nextstepaction"]
> [使用 Azure 搜索为 Azure Blob 存储中的文档编制索引](../../search/search-howto-indexing-azure-blob-storage.md)
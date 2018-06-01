---
title: Azure 媒体服务中的资产 | Microsoft Docs
description: 本文介绍何为资产以及 Azure 媒体服务如何使用这些资产。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 76ed74f2df62d478b83e109a492977ec2d580198
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305083"
---
# <a name="assets"></a>资产

**资产**包含数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）以及这些文件的相关元数据。 数字文件在上传到资产中后，即可用于媒体服务编码和流式处理工作流。

资产会被映射到 [Azure 存储帐户](storage-account-concept.md)中的 blob 容器，而资产中的文件会作为块 blob 存储在该容器中。 可以使用存储 SDK 客户端与容器中的资产文件进行交互。

当帐户使用常规用途 v2 (GPv2) 存储时，Azure 媒体服务支持 Blob 层。 借助 GPv2，可以将文件移动到冷存储。 冷存储适合存档不再需要的源文件（例如，编码后的源文件）。

在媒体服务 v3 中，可以从资产或 HTTP URL 创建作业输入。 若要创建可用作作业输入的资产，请参阅[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。

此外，请参阅[媒体服务中的存储帐户](storage-account-concept.md)和[转换和作业](transform-concept.md)。

## <a name="asset-definition"></a>资产定义

下表显示了资产的属性并给出了它们的定义。

|名称|Type|说明|
|---|---|---|
|ID|字符串|资源的完全限定的资源 ID。|
|名称|字符串|资源的名称。|
|properties.alternateId |字符串|资产的备用 ID。|
|properties.assetId |字符串|资产 ID。|
|properties.container |字符串|资产 blob 容器的名称。|
|properties.created |字符串|资产的创建日期。|
|properties.description |字符串|资产说明。|
|properties.lastModified |字符串|上次修改资产的日期。|
|properties.storageAccountName |字符串|存储帐户的名称。|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |资产加密格式。 无格式或 MediaStorageEncryption。|
|type|字符串|资源的类型。|

有关完整定义，请参阅[资产](https://docs.microsoft.com/rest/api/media/assets)。

## <a name="filtering-ordering-and-paging-support"></a>筛选、排序和分页支持

媒体服务支持对资产使用以下 OData 查询选项： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>筛选/排序

下表显示了这些选项如何应用于资产属性： 

|名称|筛选器|顺序|
|---|---|---|
|ID|支持：<br/>等于<br/>大于<br/>小于|支持：<br/>升序<br/>降序|
|名称|||
|properties.alternateId |支持：<br/>等于||
|properties.assetId |支持：<br/>等于||
|properties.container |||
|properties.created|支持：<br/>等于<br/>大于<br/>小于|支持：<br/>升序<br/>降序|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

以下 C# 示例按创建日期筛选：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>分页

已启用的四个排序顺序均支持分页。 

如果查询响应包含许多（当前超过 1000）项，该服务会返回“@odata.nextLink”属性以获取下一页结果。 这可用于逐页浏览整个结果集。 用户不能配置页面大小。 

如果在逐页浏览集合时创建或删除资产，则会在返回的结果中反映此更改（如果这些更改位于集合中尚未下载的部分）。 

以下 C# 示例显示如何枚举帐户中的所有资产。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

有关 REST 示例，请参阅 [Assets - List](https://docs.microsoft.com/rest/api/media/assets/list)（资产 - 列出）

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [流式传输文件](stream-files-dotnet-quickstart.md)

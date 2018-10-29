---
title: Azure 媒体服务中的内容密钥策略 | Microsoft Docs
description: 本文介绍了何为内容密钥策略以及 Azure 媒体服务如何使用这些终结点。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 9a5ef8df9b1ca87430fb5e8d1da94f1899c4a856
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985856"
---
# <a name="content-key-policies"></a>内容密钥策略

可以使用 Azure 媒体服务在媒体从计算机离开到存储、处理和传送的整个过程中确保其安全。 借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。

在 Azure 媒体服务 v3 中，使用内容密钥策略可以指定内容密钥如何通过媒体服务密钥传送组件传送到最终客户端。 有关详细信息，请参阅[内容保护概述](content-protection-overview.md)。

## <a name="contentkeypolicies-definition"></a>ContentKeyPolicies 定义

下表显示了 ContentKeyPolicy 的属性并给出了它们的定义。

|名称|类型|说明|
|---|---|---|
|id|字符串|资源的完全限定的资源 ID。|
|名称|字符串|资源的名称。|
|properties.created |字符串|策略的创建日期|
|properties.description |字符串|策略的说明。|
|properties.lastModified    |字符串|策略的上次修改日期|
|properties.options |ContentKeyPolicyOption[]|密钥策略选项。|
|properties.policyId    |字符串|旧版策略 ID。|
|type   |字符串|资源的类型。|

有关完整定义，请参阅[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)。

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

媒体服务支持对 ContentKeyPolicies 使用以下 OData 查询选项： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

运算符说明：

* Eq = 等于
* Ne = 不等于
* Ge = 大于或等于
* Le = 小于或等于
* Gt = 大于
* Lt = 小于

### <a name="filteringordering"></a>筛选/排序

下表显示了可以如何将这些选项应用于 StreamingPolicy 属性： 

|名称|筛选器|顺序|
|---|---|---|
|id|||
|名称|Eq、ne、ge、le、gt、lt|升序和降序|
|properties.created |Eq、ne、ge、le、gt、lt|升序和降序|
|properties.description |Eq、ne、ge、le、gt、lt||
|properties.lastModified    |Eq、ne、ge、le、gt、lt|升序和降序|
|properties.options |||
|properties.policyId    |Eq、ne||
|type   |||

### <a name="pagination"></a>分页

已启用的四个排序顺序均支持分页。 当前，页面大小为 10。

> [!TIP]
> 应始终使用下一个链接来枚举集合，而不依赖特定的页面大小。

如果查询响应包含许多项，则服务将返回一个“\@odata.nextLink”属性来获取下一页结果。 这可用于逐页浏览整个结果集。 无法配置页面大小。 

如果在逐页浏览集合时创建或删除 StreamingPolicy，则会在返回的结果中反映此更改（如果这些更改位于集合中尚未下载的部分）。 

以下 C# 示例显示如何枚举帐户中的所有 ContentKeyPolicies。

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

有关 REST 示例，请参阅[内容密钥策略 - 列表](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>后续步骤

[使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)

[使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)

---
title: 自定义子域
titleSuffix: Azure Cognitive Services
description: 每个认知服务资源的自定义子域名都是通过 Azure 门户、Azure Cloud Shell 或 Azure CLI 创建的。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: c7dd916b27cd8005162c09f7e6a090293e336719
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590632"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>认知服务的自定义子域名

Azure 认知服务对通过[Azure 门户](https://portal.azure.com)、 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)创建的每个资源使用自定义子域名称。 不同于特定 Azure 区域中所有客户经常使用的区域终结点，自定义子域名对于资源是唯一的。 需要使用自定义子域名来启用 Azure Active Directory (Azure AD) 等功能进行身份验证。

## <a name="how-does-this-impact-existing-resources"></a>这会对现有资源造成怎样的影响？

在 2019 年 7 月 1 日之前创建的认知服务资源将使用关联服务的区域终结点。 这些终结点将适用于现有资源和新资源。

若要迁移现有资源来利用自定义子域名，以便能够启用 Azure AD 等功能，请按以下说明操作：

1. 登录到 Azure 门户，找到要将自定义子域名添加到的认知服务资源。
2. 在“概述”边栏选项卡中，找到并选择“生成自定义域名”。  
3. 此时会打开一个面板，其中包含有关为资源创建唯一自定义子域的说明。
   > [!WARNING]
   > 创建自定义子域名后，**无法**对其进行更改。

## <a name="do-i-need-to-update-my-existing-resources"></a>是否需要更新现有的资源？

否。 区域终结点仍旧适用于新的和现有的认知服务，而自定义子域名是可选的。 即使添加了自定义子域名，区域终结点也仍适用于该资源。

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>如果 SDK 要求提供资源的区域，该怎么办？

> [!WARNING]
> 语音服务目前**不**支持自定义子域。 在使用语音服务和关联的 SDK 时，请使用区域终结点。

区域终结点和自定义子域名均受支持，且可换用。 但是，必须提供完整的终结点。

[Azure 门户](https://portal.azure.com)中资源的“概述”边栏选项卡内提供了区域信息。  有关区域终结点的完整列表，请参阅[是否有区域终结点的列表？](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>自定义子域名是否是区域性的？

是的。 使用自定义子域名不会改变认知服务资源的任何区域性质。

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>自定义子域名的要求是什么？

自定义子域名对于资源是唯一的。 该名称只能包含字母数字字符和 `-` 字符；长度为 2 到 64 个字符，不能以 `-` 结尾。

## <a name="can-i-change-a-custom-domain-name"></a>是否可以更改自定义域名？

否。 创建自定义子域名称并将其关联到资源后，无法对其进行更改。

## <a name="can-i-reuse-a-custom-domain-name"></a>是否可以重复使用某个自定义域名？

每个自定义子域名都是唯一的，因此，若要重复使用已分配给认知服务资源的自定义子域名，需要删除现有的资源。 删除资源后，即可重复使用该自定义子域名。

## <a name="is-there-a-list-of-regional-endpoints"></a>是否有区域终结点的列表？

是的。 下面提供了可用于 Azure 认知服务资源的区域终结点列表。

> [!NOTE]
> 转换器服务和必应搜索 API 使用全局终结点。

| 终结点类型 | 区域 | 终结点 |
|---------------|--------|----------|
| 公用 | 全局（Translator & 必应） | `https://api.cognitive.microsoft.com` |
| | 澳大利亚东部 | `https://australiaeast.api.cognitive.microsoft.com` |
| | 巴西南部 | `https://brazilsouth.api.cognitive.microsoft.com` |
| | 加拿大中部 | `https://canadacentral.api.cognitive.microsoft.com` |
| | 美国中部 | `https://centralus.api.cognitive.microsoft.com` |
| | 东亚 | `https://eastasia.api.cognitive.microsoft.com` |
| | 美国东部 | `https://eastus.api.cognitive.microsoft.com` |
| | 美国东部 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | 法国中部 | `https://francecentral.api.cognitive.microsoft.com` |
| | 印度中部 | `https://centralindia.api.cognitive.microsoft.com` |
| | 日本东部 | `https://japaneast.api.cognitive.microsoft.com` |
| | 韩国中部 | `https://koreacentral.api.cognitive.microsoft.com` |
| | 美国中北部 | `https://northcentralus.api.cognitive.microsoft.com` |
| | 北欧 | `https://northeurope.api.cognitive.microsoft.com` |
| | 南非北部 | `https://southafricanorth.api.cognitive.microsoft.com` |
| | 美国中南部 | `https://southcentralus.api.cognitive.microsoft.com` |
| | 东南亚 | `https://southeastasia.api.cognitive.microsoft.com` |
| | 英国南部 | `https://uksouth.api.cognitive.microsoft.com` |
| | 美国中西部 | `https://westcentralus.api.cognitive.microsoft.com` |
| | 西欧 | `https://westeurope.api.cognitive.microsoft.com` |
| | 美国西部 | `https://westus.api.cognitive.microsoft.com` |
| | 美国西部 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov 弗吉尼亚州 | `https://virginia.api.cognitive.microsoft.us` |
| 中国 | 中国东部 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 中国北部 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>另请参阅

* [什么是认知服务？](Welcome.md)
* [身份验证](authentication.md)

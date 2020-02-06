---
title: 自定义子域
titleSuffix: Azure Cognitive Services
description: 每个认知服务资源的自定义子域名称是通过 Azure 门户、Azure Cloud Shell 或 Azure CLI 创建的。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647693"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>认知服务的自定义子域名称

Azure 认知服务对通过[Azure 门户](https://portal.azure.com)、 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)创建的每个资源使用自定义子域名称。 不同于特定 Azure 区域中的所有客户都经常使用的区域终结点，自定义子域名称对于资源是唯一的。 需要自定义子域名称才能启用 Azure Active Directory （Azure AD）等功能进行身份验证。

## <a name="how-does-this-impact-existing-resources"></a>这如何影响现有的资源？

2019年7月1日之前创建的认知服务资源将使用关联服务的区域终结点。 这些终结点将适用于现有和新资源。

如果要迁移现有资源来利用自定义子域名称，以便能够启用 Azure AD 等功能，请遵循以下说明：

1. 登录到 Azure 门户，找到想要将自定义子域名称添加到其中的认知服务资源。
2. 在 "**概述**" 边栏选项卡中，找到并选择 "**生成自定义域名**"。
3. 这会打开一个面板，其中包含为资源创建唯一自定义子域的说明。
   > [!WARNING]
   > 创建自定义子域名称后，**不能**对其进行更改。

## <a name="do-i-need-to-update-my-existing-resources"></a>是否需要更新现有的资源？

不是。 区域终结点将继续适用于新的和现有的认知服务，自定义子域名称是可选的。 即使添加了自定义子域名称，区域终结点仍将继续使用该资源。

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>如果 SDK 要求我提供资源的区域，该怎么办？

> [!WARNING]
> 语音服务目前**不**支持自定义子域。 使用语音服务和关联的 Sdk 时，请使用区域终结点。

区域终结点和自定义子域名均受支持，可互换使用。 但是，完整终结点是必需的。

[Azure 门户](https://portal.azure.com)中资源的 "**概述**" 边栏选项卡中提供区域信息。 有关区域终结点的完整列表，请参阅[是否有区域终结点列表？](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>自定义子域名称区域？

是的。 使用自定义子域名称不会更改认知服务资源的任何区域部分。

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>自定义子域名称的要求是什么？

自定义子域名称对资源是唯一的。 名称只能包含字母数字字符和 `-` 字符;长度必须介于2到64个字符之间，并且不能以 `-`结尾。

## <a name="can-i-change-a-custom-domain-name"></a>能否更改自定义域名？

不是。 创建自定义子域名称并将其与资源关联后，不能对其进行更改。

## <a name="can-i-reuse-a-custom-domain-name"></a>是否可以重复使用自定义域名？

每个自定义子域名称都是唯一的，因此，若要重复使用已分配给认知服务资源的自定义子域名称，则需要删除现有资源。 删除资源后，可以重复使用自定义子域名。

## <a name="is-there-a-list-of-regional-endpoints"></a>是否有区域终结点列表？

是的。 这是可用于 Azure 认知服务资源的区域终结点列表。

> [!NOTE]
> 文本翻译 API 和必应搜索 API 使用全局终结点。

| 终结点类型 | 区域 | 端点 |
|---------------|--------|----------|
| 公共 | Global （文本翻译 & 必应） | `https://api.cognitive.microsoft.com` |
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
| 美国政府 | 美国政府弗吉尼亚州 | `https://virginia.api.cognitive.microsoft.us` |
| 中国 | 中国东部 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 中国北部 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>另请参阅

* [什么是认知服务？](Welcome.md)
* [身份验证](authentication.md)

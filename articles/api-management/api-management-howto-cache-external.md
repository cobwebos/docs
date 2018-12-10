---
title: 在 Azure API 管理中使用外部缓存 | Microsoft Docs
description: 了解如何在 Azure API 管理中配置和使用外部缓存。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: f57b6b35ffff85aad4d970cf9aa908d2a80eadf1
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620884"
---
# <a name="use-an-external-redis-cache-in-azure-api-management"></a>在 Azure API 管理中使用外部 Redis 缓存

除了利用内置缓存之外，Azure API 管理还允许将响应缓存在外部 Redis 缓存中。

使用外部缓存可以克服内置缓存的一些限制。 如果希望达到以下目的，则它非常有用：

* 避免在执行 API 管理更新期间定期清除缓存
* 更好地控制你的缓存配置
* 缓存比 API 管理层允许的数据更多的数据
* 将缓存与 API 管理的消耗层配合使用

若要更详细地了解缓存，请参阅 [API 管理缓存策略](api-management-caching-policies.md)和 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。

![将自己的缓存带到 APIM](media/api-management-howto-cache-external/overview.png)

学习内容：

> [!div class="checklist"]
> * 在 API 管理中添加外部缓存

## <a name="availability"></a>可用性

> [!NOTE]
> 此功能当前仅在 Azure API 管理的“消耗”层中可用。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

+ [创建一个 Azure API 管理实例](get-started-create-service-instance.md)
+ 了解 [Azure API 管理中的缓存](api-management-howto-cache.md)

## <a name="create-cache"> </a> 创建 Azure Redis 缓存

本部分介绍了如何在 Azure 中创建 Redis 缓存。 如果你在 Azure 内部或外部已有 Redis 缓存，则可以<a href="#add-external-cache">跳到</a>下一部分。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>添加外部缓存

按照以下步骤在 Azure API 管理中使用外部 Redis 缓存。

![将自己的缓存带到 APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> “使用以下位置中的”设置指定当 API 管理存在多区域配置时，哪个 API 管理区域部署将与所配置的缓存进行通信。 指定为**默认**的缓存将替代为具有区域值的缓存。
>
> 例如，如果 API 管理承载在“美国东部”、“东南亚”和“西欧”，并且配置了两个缓存，一个用于**默认**，另一个用于**东南亚**，则**东南亚**中的 API 管理将使用其自己的缓存，而其他两个区域将使用**默认**缓存项。

### <a name="add-an-azure-redis-cache-from-the-same-subscription"></a>从同一订阅中添加 Azure Redis 缓存

1. 在 Azure 门户中浏览到你的 API 管理实例。
2. 从左侧的菜单中选择“外部缓存”选项卡。
3. 单击“**+ 添加**”按钮。
4. 从“缓存实例”下拉字段中选择你的缓存。
5. 在“使用以下位置中的”下拉字段中选择“默认”或指定所需的区域。
6. 单击“ **保存**”。

### <a name="add-a-redis-cache-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>添加位于当前 Azure 订阅外部或 Azure 外部（后者为一般情况）的 Redis 缓存

1. 在 Azure 门户中浏览到你的 API 管理实例。
2. 从左侧的菜单中选择“外部缓存”选项卡。
3. 单击“**+ 添加**”按钮。
4. 从“缓存实例”下拉字段中选择“自定义”。
5. 在“使用以下位置中的”下拉字段中选择“默认”或指定所需的区域。
6. 在“连接字符串”字段中提供你的 Redis 缓存连接字符串。
7. 单击“ **保存**”。

## <a name="use-the-external-cache"></a>使用外部缓存

在 Azure API 管理中配置外部缓存后，可以通过缓存策略使用该缓存。 有关详细步骤，请参阅[添加缓存以提高 Azure API 管理中的性能](api-management-howto-cache.md)。

## <a name="next-steps"></a>后续步骤
* 有关缓存策略的详细信息，请参阅 [API 管理策略参考][API Management policy reference]中的[缓存策略][Caching policies]。
* 有关使用策略表达式按密钥缓存项目的信息，请参阅 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
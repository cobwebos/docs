---
title: "使用 Azure 门户创建 Azure 搜索服务 | Microsoft Docs"
description: "了解如何使用 Azure 门户预配 Azure 搜索服务。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: d5800e53349c3f2f6146a7d62381a531537dba90
ms.openlocfilehash: 990c679bc9937b3a684a7933fb7b8656eb798b69


---
# <a name="create-an-azure-search-service-using-the-azure-portal"></a>使用 Azure 门户创建 Azure 搜索服务
本指南介绍了使用 [Azure 门户](https://portal.azure.com/)创建（或预配）Azure 搜索服务的过程。

本指南假设你已具有可登录到 Azure 门户的 Azure 订阅。

## <a name="find-azure-search-in-the-azure-portal"></a>在 Azure 门户中找到 Azure 搜索
1. 转到 [Azure 门户](https://portal.azure.com/)并登录。
2. 单击左上角的加号（“+”）。
3. 选择“Web + 移动”。
4. 选择“Azure 搜索”。

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>为你的服务选取服务名称和 URL 终结点
1. 你的服务名称将为 Azure 搜索服务的终结点 URL 的一部分，你可以针对该终结点 URL 进行 API 调用以管理和使用搜索服务。
2. 在 **URL** 字段中键入你的服务名称。 服务名称：
   * 必须仅包含小写字母、数字或短划线（“-”）
   * 不能将短划线（“-”）用作前两个字符或最后一个字符
   * 不能包含连续短划线（“-”）
   * 长度限制为 2 至 60 个字符

## <a name="select-a-subscription-where-you-will-keep-your-service"></a>选择将在其中保留你的服务的订阅
如果你有多个订阅，可以选择哪一个将包括此 Azure 搜索服务。

## <a name="select-a-resource-group-for-your-service"></a>为你的服务选择资源组
创建新的资源组或选择现有资源组。 资源组是可结合使用的 Azure 服务和资源的集合。 例如，如果你使用 Azure 搜索编制 SQL 数据库索引，则这两个服务应属于同一资源组。

## <a name="select-the-location-where-your-service-will-be-hosted"></a>选择将托管你的服务的位置
作为 Azure 服务，Azure 搜索可托管在世界各地的数据中心中。 请注意，[价格因地域而异](https://azure.microsoft.com/pricing/details/search/)。

## <a name="select-your-pricing-tier"></a>选择你的定价层
[Azure 搜索当前以多个定价层提供](https://azure.microsoft.com/pricing/details/search/)：免费、基本或标准。 每个层都有自己的[容量和限制](search-limits-quotas-capacity.md)。 有关相关指南，请参阅[选择定价层或 SKU](search-sku-tier.md)。

在这种情况下，我们已为服务选择了标准层。

## <a name="select-the-create-button-to-provision-your-service"></a>选择“创建”按钮预配你的服务
![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>扩展你的服务
预配服务后，你可以对其进行扩展以满足你的需求。 如果你已为 Azure 搜索服务选择标准层，可采用两个维度扩展你的服务：副本和分区。 如果你已选择基本层，可以仅添加副本。

***分区***允许你的服务存储和搜索更多文档。

***副本***允许你的服务处理更高负载的搜索查询 - [服务需要 2 个副本实现只读 SLA，需要 3 个副本实现读/写 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. 转到 Azure 门户中的 Azure 搜索服务的管理边栏选项卡。
2. 在“设置”边栏选项卡中，选择“扩展”。
3. 通过添加副本或分区，扩展你的服务。
   * 每个服务层对于单个服务中允许的搜索单位总数都有不同的[限制](search-limits-quotas-capacity.md)（副本 * 分区 = 搜索单位总数）。

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>下一步
预配 Azure 搜索服务后，即可[定义 Azure 搜索索引](search-what-is-an-index.md)，从而上传和搜索你的数据。

有关快速教程，请参阅[开始在 Azure 门户中使用 Azure 搜索](search-get-started-portal.md)。




<!--HONumber=Nov16_HO5-->



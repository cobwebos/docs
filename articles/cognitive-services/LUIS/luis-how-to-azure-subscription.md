---
title: 管理 Azure 终结点订阅 | Microsoft Docs
description: 本文中将为 LUIS 帐户创建计量终结点密钥，以在拥有付费计划后为终结点提供无限流量。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: diberry
ms.openlocfilehash: 0b735499ae589e44c2ce5076fce38ec47ddd69c7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223273"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>管理 Azure 终结点订阅密钥

如果仅用于测试和原型，请使用免费 (F0) 层。 对于生产系统，请使用[付费](https://aka.ms/luis-price-tier)层。 

> [!NOTE]
> 不要将[创作密钥](luis-concept-keys.md#authoring-key)用于生产中的终结点查询。

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>创建 LUIS 终结点密钥

1. 登录 [Microsoft Azure](https://ms.portal.azure.com/) 
2. 单击左上角面板中的绿色 + 符号，在商城中搜索“LUIS”，然后单击“语言理解”，按照创建步骤创建 LUIS 订阅帐户。 

    ![Azure 搜索](./media/luis-azure-subscription/azure-search.png) 

3. 为订阅配置帐户名、定价层等设置。 

    ![Azure API 选择](./media/luis-azure-subscription/azure-api-choice.png) 

4. 创建 LUIS 服务后，可选择“资源管理”->“密钥”，查看生成的密钥。  

    ![Azure 密钥](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > * 登录所在地区的 [LUIS](luis-reference-regions.md) 网站，然后[添加新的 LUIS 终结点密钥](luis-how-to-manage-keys.md#assign-endpoint-key)。 
    > * 需要记住创建的 Azure 服务的名称，以便在所在地区的 [LUIS](luis-reference-regions.md) 发布页中将其选中。  

## <a name="change-luis-pricing-tier"></a>更改 LUIS 定价层

1.  在 [Azure](https://portal.azure.com) 中查找你的 LUIS 订阅。 单击该 LUIS 订阅。
    ![查找 LUIS 订阅](./media/luis-usage-tiers/find.png)
2.  单击“定价层”，查看可用的定价层。 
    ![查看定价层](./media/luis-usage-tiers/subscription.png)
3.  单击定价层，然后单击“选择”以保存更改。 
    ![更改 LUIS 支付层](./media/luis-usage-tiers/plans.png)
4.  定价更改完成后，页面将出现一个供于验证新定价层的弹出窗口。 
    ![验证 LUIS 支付层](./media/luis-usage-tiers/updated.png)
5. 请记住在“发布”页[分配此终结点密钥](luis-how-to-manage-keys.md#assign-endpoint-key)，并将其用于所有终结点查询。 

## <a name="exceed-pricing-tier-usage"></a>超出定价层用量
每层允许以特定速率向 LUIS 帐户发送终结点请求。 如果请求速率高于计费帐户的每分钟或每月的允许速率，则请求会出现 HTTP 错误“429: 请求过多”。

每层允许按月累计请求数。 如果总请求数大于允许的速率，则请求会出现 HTTP 错误“403: 禁止”。  

## <a name="viewing-summary-usage"></a>查看使用概况
可在 Azure 中查看 LUIS 使用情况信息。 “概述”页显示包含调用和错误在内的最新摘要信息。 如果发出 LUIS 终结点请求并立即查看“概述”页，则最多需要五分钟才会显示使用情况。

![查看使用概况](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>自定义使用情况图表
通过指标可更加详细地了解数据。

![默认指标](./media/luis-usage-tiers/metrics-default.png)

可针对时间期限和指标类型配置度量值图表。 

![自定义指标](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果希望在达到特定事务阈值（例如 10,000 个事务）时收到通知，则可以创建警报。 

![默认警报](./media/luis-usage-tiers/alert-default.png)

添加针对特定时间段内“总调用数”指标的指标警报。 添加应接收该警报的所有人员的电子邮件地址。 添加应接收该警报的所有系统的 Webhook。 还可在触发警报时运行逻辑应用。 

## <a name="next-steps"></a>后续步骤

了解如何使用[版本](luis-how-to-manage-versions.md)管理对 LUIS 应用的更改。
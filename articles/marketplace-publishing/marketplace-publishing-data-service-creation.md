---
title: "为应用商店创建数据服务指南 | Microsoft Docs"
description: "详细说明如何创建、验证和部署在 Azure 应用商店上提供购买的数据服务。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0057a5e8ab3d2fa3c9f201ec88246db7a53cc4a


---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Azure 应用商店的数据服务发布指南
> [!IMPORTANT]
> **本次我们将不再载入任何新的数据服务发布服务器。新的 dataservices 将不会获准出现在列表中。** 如果想要在 AppSource 上发布 SaaS 业务应用程序，可以在[此处](https://appsource.microsoft.com/partners)找到更多信息。 如果想要在 Azure 应用商店上发布 IaaS 应用程序或开发人员服务，可以在[此处](https://azure.microsoft.com/marketplace/programs/certified/)找到更多信息。
> 
> 

在完成第 1 步“[帐户创建和注册](marketplace-publishing-accounts-creation-registration.md)”后，我们将指导你浏览与 Azure 应用商店的数据服务产品有关的[General Non-Technical](marketplace-publishing-pre-requisites.md)（非技术性一般问题）和[Technical Requirements](marketplace-publishing-data-service-creation-prerequisites.md)（技术要求）。 现在，我们将引导你在 Azure 应用商店的[发布门户][link-pubportal] 创建数据服务供应项。

## <a name="1-login-to-the-publishing-portal"></a>1.  登录发布门户。
前往 [https://publish.windowsazure.com](https://publish.windowsazure.com.)

**第一次登录发布门户时，使用的帐户应与你的公司在开发人员中心中注册卖方配置文件时所使用的帐户相同。**  （稍后可以添加你的公司的任何员工作为发布门户中的共同管理员）。

如果这是首次登录到发布门户，单击“**发布数据服务**”磁贴。

## <a name="2-choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.  选择左侧导航菜单中的“**数据服务**”。
  ![绘制](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3-create-a-new-data-service"></a>3.  创建新数据服务
为新数据服务供应项填写标题，然后单击右侧的 "+"。

  ![绘制](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4-review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.  在导航菜单中，复查新创建的数据服务下面的子菜单。
单击“**演练**”选项卡并查看正确发布 Azure 应用商店上的数据服务所需的所有必要步骤。

> [!TIP]
> 始终可以单击"演练"页中的链接或使用左侧数据服务供应项的子菜单上的选项卡。
> 
> 

## <a name="5-create-a-new-plan"></a>5.  创建新计划。
### <a name="offers-plans-transactions"></a>产品、 计划、 事务。
每个供应项可以有多个计划，但必须具有至少一 （1）个 计划。 最终用户在订阅你的产品时，他们所订阅的其实是其中的一个供应项计划。 每个计划定义最终用户将如何能够使用你的服务。

对于数据服务，Azure 应用商店当前仅支持按月订阅事务模式，即最终用户将根据其订阅的特定计划的价格按月付费，并可以消费由计划所定义的每个月的事务数。

每个事务通常定义为你的数据服务基于发送到服务的查询将返回的记录数。 默认值为 100。 将记录数除以 100 并向上舍入到最接近的整数即为返回到每个查询的事务数。

Azure Marketplace 服务层将负责监视每个查询消耗的事务数。

> [!IMPORTANT]
> 如果最终用户达到其当月的事务数限制，将被阻止继续使用服务，直到每月的订阅周期结束。
> 
> 计划或其中的一个计划可以（但不是必须）包含无限个事务数。
> 
> 

### <a name="create-a-plan"></a>创建一个计划。
1. 单击 **"+"** 旁边的 "Add a new plan"（添加新计划）。
2. 为计划选择其中一个使用选项：**Unlimited**（无限制）或 **Limited**（有限制） 。  如果选择 Limited，则提供计划允许在一个月内消耗的事务数。
   
    ![绘制](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    发布门户还建议使用“计划标识符”，它将用来向最终用户传达在 UI 中的计划名称，应用商店服务也会使用其来标识计划。 如果需要，可以更改“计划标识符”。
   
   > [!NOTE]
   > "计划标识符"必须在每个供应项范围内是唯一的。 由于发布门户中还有许多其他使用的标识符，因此在首次发布到生产后，计划标识符会被锁定，你将不能更改此标识符。
   > 
   > 
3. 单击以接受你的选择。
4. 然后会要求你问一些与你新创建的计划有关的其他问题。
   
    ![绘制](media/marketplace-publishing-data-service-creation/step-5.2.png)

| 问题 | 含义 |
| --- | --- |
| **此计划免费且适用于全球范围内吗？** |您可以创建完全免费的计划。 如果它是此供应项的唯一计划 - 则意味着你将在应用商店发布“免费供应项”。 如果它只是一个（或几个中的一个）计划，它会提供一个选项，让最终用户每月通过消耗相对较少的事务数来了解有关你的服务的详细信息。  如果回答是“是”，则无需提出进一步的问题。 |

> [!NOTE]
> 最终用户始终可以升级到付费计划。
> 
> 

| 问题 | 含义 |
| --- | --- |
| **是否提供免费试用版？** |可以选择"无试用版"，或者提供可使用计划“一个月”的选项。 发布者喜欢使用此选项让最终用户能够在免费的一个月内了解供应项的优点。 |

> [!IMPORTANT]
> 最终用户仅能在已确定付款方式，例如信用卡，企业协议时，才能购买免费试用版。
> 
> 一个月后的免费试用过后，Azure 应用商店将从订阅日期开始按价格对客户计费，除非客户取消了订阅。 不会为最终用户提供任何特殊的通知。
> 
> 

| 问题 | 含义 |
| --- | --- |
| **此计划需要促销代码才能购买吗？** |发布者可以通过提供一个名为 "Promocode" 的特殊代码给特定的客户，以限制对服务计划的访问。 仅具有此 Promocode 的最终用户才能够订阅计划。 如果选择“否”，则你同意在该供应项可用的区域中，每位用户均可以订阅此计划（请参阅“[应用商店市场营销内容指南](marketplace-publishing-push-to-staging.md)”以了解多详细信息）。 将不会提出进一步的问题。 |
| **另外对于没有有效促销代码的人，是否要隐藏此计划？** |如果前一个问题的答案是“是”，发布者可以选择将此计划从应用商店用户界面完全删除。 这意味着，客户不会在供应项详细信息页面看到此计划。 接收到 promocode 来购买计划的最终用户可以使用该代码订阅计划。 |

## <a name="6-create-your-marketplace-marketing-content"></a>6.  创建应用商店市场营销内容
有关如何在**市场营销、定价、支持和类别**选项卡提供所需的信息，请访问[应用商店市场营销内容指南](marketplace-publishing-push-to-staging.md)，该指南适用于 Azure 应用商店中发布的所有项目。  

## <a name="7-connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.  将你的供应项与你的服务关联（基于 SQL Azure 或 Web 服务）。
单击“**数据服务**”子菜单。

在该页的上半部分将会要求你提供此供应项的 **Namespace**。  

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.png)

以下问题将定义发布者如何将新创建的供应项公布到 Azure 应用商店。 （有关更多详细信息，请参阅[数据服务技术要求指南](marketplace-publishing-data-service-creation-prerequisites.md)。

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.2.png)

**发布基于数据库的服务**

单击“**数据库**”。 将显示以下页面：

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.3.png)

若要为基于 SQL Azure DB 的数据集创建 CSDL 映射：

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.4.png)

然后针对每个表

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.6.png)

如果是 Web 服务

  ![绘制](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> 阅读[通过 CSDL 将现有的 web 服务映射到 OData](marketplace-publishing-data-service-creation-odata-mapping.md) 了解关于创建 CSDL Web 服务的说明和示例。
> 
> 

## <a name="next-steps"></a>后续步骤
现在你已经创建数据服务产品，在移至[在过渡环境中测试数据服务](marketplace-publishing-data-service-test-in-staging.md)之前，请确保完成[市场营销内容指南](marketplace-publishing-push-to-staging.md)中的说明。

## <a name="see-also"></a>另请参阅
* [入门教程：如何将供应项发布到 Azure 应用商店](marketplace-publishing-getting-started.md)
* 如果你有兴趣了解全面的 OData 映射过程和用途，请阅读[数据服务 OData 映射](marketplace-publishing-data-service-creation-odata-mapping.md)一文以查看定义、结构和说明。
* 如果你有兴趣学习和了解特定节点及其参数，请阅读[数据服务 OData 映射节点](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)一文以了解有关定义和说明、 示例和使用案例上下文。
* 如果你有兴趣查看示例，请阅读[数据服务 OData 映射示例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文以查看示例代码，了解代码的语法和上下文。

[link-pubportal]:https://publish.windowsazure.com



<!--HONumber=Nov16_HO3-->



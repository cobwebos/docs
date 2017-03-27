---
title: "设置并使用机器学习建议 API | Microsoft Docs"
description: "通过 Azure 机器学习生成的 Microsoft 建议 API 常见问题"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 1ffc3c16-e040-4225-9d72-105129938dfa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 9ac2a1bf5987fc6fc30e20a1b4a10340eeed3825
ms.openlocfilehash: 15cf891b9c31e1f2e2b108e631f32883ce2f36a7


---
# <a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>有关设置和使用机器学习建议 API 的常见问题
**什么是建议？**

> [!NOTE]
> 应开始使用建议 API 认知服务，而非此版本。 建议认知服务将替代此服务，并且所有新功能都将在该处开发。 它具有新功能，如支持批处理、更好用的 API 资源管理器、更简洁的 API 图面、更一致的注册/计费体验等。
> 了解有关[迁移到新认知服务](http://aka.ms/recomigrate)的详细信息
> 
> 

对于依赖建议向客户交叉销售及促销产品和服务的组织和公司，Azure 机器学习中的建议提供自助建议引擎。 它实现了将矩阵因子分解用作核心算法的协同筛选。 应用程序开发人员可使用 REST API 访问 RECOMMENDATIONS。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**我可以怎样使用建议？**

建议采用输入作为项目或项目集，并返回相关建议列表。 例如：在线零售商的客户单击一款产品。 该在线零售商将该产品作为输入发送到建议、转而获取产品列表，然后决定向客户显示的产品。 你可能想要使用建议优化在线商店或者甚至是通知内部销售部门或呼叫中心。

**是否有任何使用限制？**

建议具有以下使用限制：

* 每个订阅的最大模型数：10
* 目录可容纳的最大项目数：100000
* 保留的最大使用点数约为&50000;00 个。 如果上传或报告新使用点，将删除最老的使用点。
* 电子邮件可发送的最大数据大小（例如导入目录数据、导入使用数据）是 200 MB
* 不活动的建议模型生成的每秒事务数 (TPS) 约为&2; TPS。 活动的建议模型生成可容纳最多 20 TPS。

## <a name="purchase-and-billing"></a>购买和计费
**启动期间建议的成本是多少？**

建议是基于订阅的服务。 收费取决于每个月的事务量。 有关定价信息，可查看 Microsoft Azure 应用商店中的[产品/服务页](https://datamarket.azure.com/dataset/amla/recommendations)。

**使建议为我跟踪和存储用户活动有任何成本吗？**

暂时不需要。

**建议是否有免费试用版？**

有免费试用版，每个月的事务数限制在 10000 个。

**什么时候支付建议费？**

付费订阅是有月费的订阅。 当你购买付费订阅时，将立即向你收取第一个月的使用费。 所收费用与订阅页上的产品/服务（以及适用税款）相关。 此月费在每个月与原始购买日期相同的日历日期收取，直到取消订阅为止。 

**如何升级到更高层的服务？**

可在 Microsoft Azure 应用商店的[产品/服务页](https://datamarket.azure.com/dataset/amla/recommendations)购买或更新订阅。

升级订阅时：

* 旧订阅剩下的事务不会添加到新订阅。 
* 你为新订阅付全款，即便旧订阅上有未使用的事务。

升级订阅的过程：

* 导航到[产品/服务页](https://datamarket.azure.com/dataset/amla/recommendations)。
* 如果尚未登录，请登录应用商店。
* 在右侧窗格中，所有可用计划均已列出。 单击要升级到的计划的单选按钮。
* 如果要升级，请单击“确定”。 如果不想要升级，请单击“取消”。

**重要提示** 请在升级前仔细阅读对话框，因为有计费和使用提示。

**建议订阅将在何时结束？**

订阅在取消时结束。 如果要取消订阅，请参阅以下说明。

**如何取消建议订阅？**

若要取消订阅，请使用以下步骤。 如果当前订阅是付费订阅，订阅将持续有效至当前计费周期结束。 如果需要取消立即生效，可在 [Microsoft 支持](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)联系我们。

**注意** 如果在计费周期结束前取消或计费周期内仍有未使用的事务，将不予退款。

* 导航到[产品/服务页](https://datamarket.azure.com/dataset/amla/recommendations)。
* 如果尚未登录，请登录应用商店。
* 单击数据集名称和状态右侧的“取消”。 可使用此订阅到当前计费周期结束或达到事务数上限（以先发生的事项为准）。

如果要立即取消订阅，以便可以购买新订阅，请在 [Microsoft 支持](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)提出票证。

## <a name="getting-started-with-recommendations"></a>建议入门
**建议适合我吗？** 

机器学习中的建议适用于依赖建议向客户交叉销售及促销产品或服务的组织和公司。 如果拥有面向客户的网站、销售人员、内部销售人员或呼叫中心，并提供超过几十个产品或服务的目录，则至少可从使用建议中获益。 

进行建议实验要相当简单。 当前基于 API 的版本需要基本的编程技能。 如果需要帮助，请联系开发网站的供应商。 如果设有内部 IT 部门或内部开发人员，他们应能够使建议为你工作。 

**设置建议的前提条件是什么？**

建议要求具有用户选择日志，因为它与目录有关。 如果没有此类日志，但却拥有面向客户的网站，建议功能可为你收集用户活动。 

建议还需要产品或服务目录。 如果没有目录，建议功能可使用实际的客户使用数据，并提取目录。 默示目录不会包含未报告为用户事务一部分的项目。

**第一次如何设置建议？**

[订阅](https://datamarket.azure.com/dataset/amla/recommendations)建议功能后，应使用 [Azure 机器学习建议 - 快速入门指南](machine-learning-recommendation-api-quick-start-guide.md)中的 API 文档设置服务。

**可在哪里找到 API 文档？** 

API 文档为 [Azure 机器学习建议 - 快速入门指南](machine-learning-recommendation-api-quick-start-guide.md)。

**将目录和使用数据上传到建议有什么建议？**

有两个选项可上传目录和使用数据：从 CRM 系统或其他日志导出数据，然后将其上传到建议，或者将标记添加到跟踪用户活动的网站。 如果使用后一种方法，数据将存储在 Azure 中。

## <a name="maintenance-and-support"></a>维护和支持
**数据集可以有多大？**

每个数据集可包含最多 100000 个目录项和最多 2048 MB 使用数据。
此外，订阅可包含最多 10 个数据集（模型）。

**在哪里可获取建议的技术支持？**

技术支持在 [Microsoft Azure 支持](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)站点上提供。

**在哪里可以找到使用条款？**

[Microsoft Azure 机器学习建议 API 服务条款](https://datamarket.azure.com/dataset/amla/recommendations#terms)。




<!--HONumber=Jan17_HO1-->



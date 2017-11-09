---
title: "数据工厂使用案例 - 产品建议"
description: "了解通过 Azure 数据工厂以及其他服务实现的使用案例。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e72dd426f7af3d1539aad6a3499d2ce5f792c152
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="use-case---product-recommendations"></a>使用案例 - 产品建议
Azure 数据工厂是用于实现解决方案加速器的 Cortana Intelligence 套件的许多服务之一。  有关此套件的详细信息，请参阅 [Cortana Intelligence 套件](http://www.microsoft.com/cortanaanalytics) 页面。 在本文档中，我们介绍了 Azure 用户通过使用 Azure 数据工厂和其他 Cortana Intelligence 组件服务已解决和实现的常见用例。

## <a name="scenario"></a>方案
为了吸引客户购买产品，在线零售商通常会向客户推介他们最有可能感兴趣的产品，从而大大提高购买几率。 为实现此目的，在线零售商需要针对特定用户使用个性化产品建议来自定义用户的在线体验。 这些个性化建议均基于其当前和以往购物行为数据、产品信息、新引入的品牌以及产品和客户分段数据。  另外，他们还可分析所有用户的整体使用行为，并以此为基础提供用户产品建议。

这些零售商的目的是优化用户“单击-销售”转换并获得更高的销售收入。  他们通过根据客户兴趣和行动提供基于行为的全背景式产品建议来实现此转化。 在此使用案例中，我们以希望优化客户购买行为的在线零售商为例。 但这些原则适用于想要通过个性化产品建议吸引客户关注其产品和服务并增强客户购买体验的任何企业。

## <a name="challenges"></a>挑战
在线零售商在尝试实现这种类型的使用案例会遇到很多挑战。 

首先，必须从本地和云中的多个数据源提取不同大小和形状数据。 此数据包括产品数据、客户历史行为数据以及用户浏览在线零售网站时的用户数据。 

第二，个性化产品建议必须合理准确地计算和预测。 除了产品、品牌、客户行为和浏览器数据外，在线零售商在为用户确定最佳产品建议时还需要考虑客户对以往购买的反馈。 

第三，建议必须立即传送给用户以提供无缝浏览和购买体验，并提供最新且相关的建议。 

最后，零售商需要通过跟踪整体追加销售和交叉销售点击转化销售成功率来衡量方法的有效性，并对未来的建议进行相应调整。

## <a name="solution-overview"></a>解决方案概述
在此示例中，实际 Azure 用户通过使用 Azure 数据工厂和其他 Cortana Intelligence 组件服务解决和实现使用案例，包括 [HDInsight](https://azure.microsoft.com/services/hdinsight/) 和 [Power BI](https://powerbi.microsoft.com/)。

在线零售商在整个工作流中将 Azure Blob 存储、本地 SQL server、Azure SQL DB 和关系数据市场用作其数据存储选项。  Blob 存储包含客户信息、客户行为数据和产品信息数据。 产品信息数据包括产品品牌信息以及以本地方式存储于 SQL 数据仓库中的产品目录。 

用户在网站上浏览目录中的产品时，所有数据会汇总起来并输送到产品建议系统以基于客户兴趣和行为提供个性化的建议。 客户还会看到另外一种与寻找的产品相关的产品，这种产品以网站整体使用模式为基础，并不是以一个用户为基础。

![使用案例图](./media/data-factory-product-reco-usecase/diagram-1.png)

在线零售商网站每天会有千兆字节的原始 web 日志文件生成为半结构化文件。 使用数据工厂的全局部署数据移动作式服务，将原始 Web 日志文件以及客户和产品目录信息定期提取到 Azure Blob 存储中。 一天的原始日志文件会在 blob 存储中进行划分归类（按年份和月份）以便长期存储。  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) 用于在 blob 存储中划分原始日志文件并通过 Hive 和 Pig 脚本处理大规模提取的日志。 已划分的 web 日志数据随后经处理以为机器学习建议系统提取需要的输入，从而生成个性化产品建议。

在此示例中，机器学习使用的建议系统是来自 [Apache Mahout](http://mahout.apache.org/) 的开源机器学习建议平台。  任何 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/) 或自定义模型均可应用于本方案。  Mahout 模型用于预测网站上项目之间的相似性（以整体使用模式为基础），以及用于基于每个用户生成个性化的建议。

最后，个性化的产品建议结果集将转移到关系数据市场中供零售商网站使用。  结果集还可由其他程序直接从 blob 存储进行访问，或移动到其他客户或使用案例的相关存储中。

## <a name="benefits"></a>优点
通过优化产品建议策略并将其与业务目标对齐，本解决方案可满足在线零售商的商品销售和市场营销目标。 此外，他们还能够以高效、可靠且经济高效的方式优化和管理产品建议工作流。 他们通过本方法可以很方便地根据销售单击转换成功率测量结果更新模型和优化效果。 通过使用 Azure 数据工厂，他们可以摆脱耗时且成本高昂的手动云资源管理，改用按需式云资源管理。 因此，他们可节省时间和资金，并减少解决方案的部署时间。 通过在 Azure 门户可用的直观式数据工厂监视和管理 UI，可以很方便地显示数据沿袭视图和操作服务运行状况并进行相关故障排除。 他们现在可以安排和管理解决方案，以便以可靠方式完成数据并呈现给用户，并且可以自动管理数据和处理依赖项，无需人工操作。

通过提供这种个性化购物体验，在线零售商创造了更具竞争力、更有吸引力的客户体验，因此增加了销售和整体客户满意度。


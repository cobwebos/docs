---
title: 使用机器学习分析客户流失 | Microsoft Docs
description: 开发集成模型对客户流失进行分析和评分的案例研究
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 1beba951a6785aa90eef22a63a8064e9da1bb27f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835111"
---
# <a name="analyzing-customer-churn-using-azure-machine-learning"></a>使用 Azure 机器学习分析客户流失
## <a name="overview"></a>概述
本文介绍使用 Azure 机器学习生成的客户流失分析项目的参考实现。 本文讨论用于整体解决行业客户流失问题的关联通用模型。 还将衡量使用机器学习生成的模型的准确性，并对进一步开发方向进行评估。  

### <a name="acknowledgements"></a>致谢
Serge Berger（Microsoft 首席数据科学家）和 Roger Barga（Microsoft Azure 机器学习前产品经理）对此实验进行了开发和测试。 Azure 文档团队有幸确认了他们的专业知识，感谢他们分享了这份白皮书。

> [!NOTE]
> 用于此实验的数据不会公开提供。 有关如何生成流失分析的机器学习模型示例，请参阅：[Azure AI 库](http://gallery.cortanaintelligence.com/)中的[零售流失模型模板](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>客户流失的问题
消费者市场和所有营业行业中的企业都必须处理客户流失问题。 有时流失过多，会影响策略决策。 传统的解决方案是预测高倾向性的流失人员，并通过接待服务、市场营销活动或应用特殊处理方式来满足其需求。 这些方法可能因行业而异。 即使行业相同（例如，电信），不同的使用者群集使用的方法也不尽相同。

常见情况是，企业需要减少对保留这些特殊客户作出的努力。 因此，一种自然的方法是根据流失可能性对每位客户进行评分，并解决前 N 名客户。 前几名客户可能是最赚钱的。 例如，在更复杂的情况下，在选择要应用特殊处理方式的客户时，将使用利润函数。 但是，这些注意事项只是处理流失问题的整体策略的一部分。 企业还必须考虑到帐户风险（以及关联的风险容差）、干预的级别和成本，以及合理的客户划分。  

## <a name="industry-outlook-and-approaches"></a>行业前景和方法
复杂的流失处理是成熟行业的符号。 经典示例是电信行业，在此行业中，订户经常从一个提供商转换为另一个提供商。 自愿流失是首要关心的问题。 此外，提供商要积累关于*流失驱动者*的重要知识，这是驱动客户转变的因素。

例如，在移动电话业务中，对手机或设备的选择是为人所熟知的流失驱动者。 结果是，为新订户补贴手机价格并向现有用户的升级收取完整价格，这成为了常用策略。 从历史上看，此策略将导致客户希望从一个提供商换为另一个提供商，以获取新的折扣。 反过来，这也会引起提供商对其策略进行优化。

手机产品的高波动性是使基于当前手机模型的流失模型快速失效的因素。 另外，手机不仅仅是电信设备，它们也是时尚宣言（如 iPhone）。 这些社会预测因素不在常规电信数据集的范围之内。

建模的最终结果是不能通过消除流失的已知原因简单制定可靠的策略。 事实上，一个持续建模策略是**必需的**，它包含了量化分类变量的经典模型（如决策树）。

通过对其客户使用大数据集，组织正在将执行大数据分析（尤其是基于大数据的流失检测）作为解决问题的有效方法。 可以在 ETL 部分的建议中找到关于流失问题的大数据解决方法的更多消息。  

## <a name="methodology-to-model-customer-churn"></a>为客户流失建模的方法
图 1-3 描述了解决客户流失常见问题的解决流程：  

1. 使用风险模型可考虑操作影响概率和风险的方式。
2. 使用干预模型可考虑干预级别影响流失概率以及客户终身价值 (CLV) 的方式。
3. 此分析有助于进行定性分析，将其升级为面向客户群的主动营销活动，以提供最佳提议。  

![][1]

此前瞻方法是处理流失的最佳方式，但是它也具有复杂性：必须开发一个多模型原型并跟踪模型间的依赖关系。 模型间的交互可进行封装，如下图所示：  

![][2]

*图 4：统一多模型原型*  

如果要将整体方法传递到客户保留，那么模型间的交互是关键。 每个模型必然会随时间推移而降低；因此，该体系结构是隐式循环（类似于按照 CRISP-DM 数据挖掘标准 [***3***] 设置的原型）。  

风险决策营销分段/分解的总体周期仍然是通用结构，这适用于许多业务问题。 流失分析只是此组问题的突出代表，因为它展示了复杂业务问题（不允许简化的预测解决方案）的所有特征。 流失的新式方法的社交方面不会在方法中突出显示，但是社交方面会封装在建模原型中，因此它也会存在于任何模型中。  

关于大数据，此处有一个有趣的补充。 目前的电信和零售企业会收集关于其客户的详尽数据，考虑到新兴趋势（如物联网和无处不在的设备，这允许企业在多层上使用智能解决方案），可以轻松预测出对多模型连接的需求将成为普遍趋势。  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>在机器学习工作室中实现建模原型
根据刚才描述的问题，实现集成建模和评分的最佳方式是什么？ 在本部分中，将演示如何使用 Azure 机器学习工作室完成操作。  

多模型方法是为流失问题设计全局原型所必不可少的。 甚至方法的评分（预测）部分也应该是多模型。  

下图显示了创建的原型，使用了四种机器学习工作室中的评分算法来预测流失。 使用多模型方法的原因不仅是为了创建集成分类器来提高准确性，还为了防止过度拟合，从而改善规范性功能选择。  

![][3]

*图 5：流失建模方法的原型*  

以下部分提供了关于原型评分模型（使用机器学习工作室实现）的详细信息。  

### <a name="data-selection-and-preparation"></a>数据选择和准备
从 CRM 垂直解决方案中获取用于生成模型和对客户评分的数据，对数据进行模糊处理以保护客户隐私。 数据包括美国大约 8,000 个订阅的信息，其中包含三个源：预配数据（订阅源数据）、活动数据（系统的使用情况），以及客户支持数据。 数据不包括任何与客户信息相关的业务；例如，不包括会员元数据或信用评分。  

为简单起见，ETL 和数据清理进程不在范围内，因为我们假定数据准备已在其他位置完成。   

建模的功能选择基于因素集的初步意义评分，包括在使用随机林模型的进程中。 对于机器学习工作室中的实现，计算了具有代表性的功能的平均值、中间值和范围。 例如，为定型数据添加了聚合值，如用于用户活动的最小或最大值。    

还捕获了最近六个月的临时数据。 我们分析了一年的数据并确立了：即使有统计意义上的重要趋势，对流失的影响也会在六个月后大大降低。  

最重要的一点是，整个流程（包括 ETL、功能选择和建模）都会使用 Microsoft Azure 中的数据源在机器学习工作室中实现。   

下图显示了所使用的数据。  

![][4]

*图 6：数据源（模糊处理）的摘录*  

![][5]

*图 7：从数据源中提取的功能*
 

> 请注意，此数据是私有的，因此不能共享模型和数据。
> 但是，有关使用公开可用数据的类似模型，请参阅 [Azure AI 库](http://gallery.cortanaintelligence.com/)中的以下示例实验：[Telco Customer Churn](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383)（电信客户流失）
> 
> 若要了解如何能使用 Cortana Intelligence 套件实现流失分析，还推荐由高级经理 Wee Hyong Tok 制作的[此视频](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html)。 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>在原型中使用的算法
使用以下四种机器学习算法生成原型（没有自定义项）：  

1. 逻辑回归 (LR)
2. 提升决策树 (BT)
3. 平均感知器 (AP)
4. 支持向量机 (SVM)  

下图演示了实验设计图面的一部分，这指示模型在其中创建的序列：  

![][6]  

*图 8：在机器学习工作室中创建模型*  

### <a name="scoring-methods"></a>评分方法
使用标记的训练数据集对四种模型进行评分。  

也会将评分数据集提交到可比较模型（使用桌面版 SAS 企业挖掘程序 12 生成）。 将衡量 SAS 模型和所有四种机器学习工作室模型的准确性。  

## <a name="results"></a>结果
在本部分中，会根据评分数据集，介绍关于模型的准确性的查找结果。  

### <a name="accuracy-and-precision-of-scoring"></a>评分的准确性和精度
通常情况下，Azure 机器学习中的实现的准确性比 SAS 低大约 10-15%（曲线下面积或 AUC）。  

但是，流失中最重要的指标是错误分类率：即，根据分类器预测的前 N 个流失者，其中哪些实际**不会**流失，但尚未收到特殊处理方式？ 下图比较了所有模型的错误分类率：  

![][7]

*图 9：Passau 原型的曲线下面积*

### <a name="using-auc-to-compare-results"></a>使用 AUC 比较结果
曲线下面积 (AUC) 是表示正值和负值的分数分布之间*可分性*的全局衡量指标。 它类似于传统的受试者工作特征 (ROC) 图，但一个重要的不同点是，AUC 指标不要求选择阈值。 相反，它会汇总**所有**可能选项的结果。 与此相反，传统的 ROC 图会显示垂直轴的阳性率和水平轴的阳性率，以及分类阈值变化。   

AUC 通常用作不同算法（或不同系统）的价值衡量，因为其允许根据模型的 AUC 值进行比较。 在行业（如气象学和生物科学）中这是常用方法。 因此，AUC 表示评估分类器性能的常用工具。  

### <a name="comparing-misclassification-rates"></a>比较错误分类率
通过使用大约 8,000 个订阅的 CRM 数据，比较了正在讨论的数据集上的错误分类率。  

* SAS 错误分类率是 10-15%。
* 针对前 200-300 位流失者，机器学习工作室错误分类率是 15-20%。  

在电信行业中，重要的一点是，通过为其提供接待服务或其他特殊处理方式，只为那些具有最高流失风险的客户解决问题。 在这方面，机器学习工作室实现获得了可与 SAS 模型相媲美的结果。  

使用相同令牌的情况下，准确性比精度更重要，因为我们针对的正是对潜在流失者进行分类。  

下图来自 Wikipedia，使用生动、易于理解的图描绘了相关关系：  

![][8]

*图 10：准确性和精度之间的权衡*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>提升决策树模型的准确性和精度结果
下表显示使用提升决策树模型（恰巧是四种模型中最准确的）的机器学习原型的评分中的原始结果：  

![][9]

*图 11：提升决策树模型特征*

## <a name="performance-comparison"></a>性能比较
我们比较了使用机器学习工作室模型评分的数据的速度，以及通过使用桌面版 SAS 企业挖掘程序 12.1 创建的可比较模型。  

下表总结了这些算法的性能：  

*表 1. 算法的常规性能（准确性）*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| 平均模型 |最佳模型 |表现不佳 |平均模型 |

机器学习工作室中托管的模型的执行速度比 SAS 快 15-25%，但是准确性在很大程度上不相上下。  

## <a name="discussion-and-recommendations"></a>讨论和建议
在电信行业中，一些用于分析流失的做法应运而生，包括：  

* 四种基本类别的派生指标：
  * **实体（例如，订阅）**。 预配关于订阅和/或客户（流失主体）的基本信息。
  * **活动**。 获取所有与实体相关的可能使用情况信息，例如，登录数量。
  * **客户支持**。 从客户支持日志中获取信息，指示订阅是否有问题或是否与客户支持进行交互。
  * **竞争和业务数据**。 获取有关客户的任何可能信息（例如，不可跟踪或难以跟踪）。
* 使用重要事项来驱动功能选择。 这意味着，提升决策树模型始终是一种可行的方法。  

使用这四个类别可创建一种效果：使用一种简单的*确定性*方法，根据每个类别的合理因素上形成的索引，足以标识有流失风险的客户。 遗憾的是，尽管这一概念似乎是合理的，但其实是错误的观念。 因为流失是暂时的效果，而导致流失的因素通常处于瞬时状态。 今天导致客户决定留下的因素可能明天就变化了，而六个月后，影响因素肯定会不一样。 因此，*概率*模型是必不可少的。  

这点需要重点注意，但在商业中却常常被忽视，商业中通常更偏向于以商业智能为导向的分析方式，主要是因为这样更易销售并且可以实现简单自动化。  

但是，通过使用机器学习工作室实现的自服务分析的承诺是，通过分支机构或部门进行分级的四种信息成为了关于流失的机器学习的价值来源。  

Azure 机器学习中即将推出的另一项令人兴奋的功能是，可将自定义模块添加到已有的预定义模块的存储库中。 从根本上来说，此功能创造了一种机会，可选择库并为垂直市场创建模板。 这是 Azure 机器学习在市场中的一项重要的区别。  

希望将来能继续本主题，尤其是与大数据分析相关的部分。
  

## <a name="conclusion"></a>结束语
此白皮书介绍了一种明智的方法，可通过使用通用框架来处理客户流失的常见问题。 确定了评分模型的原型，并通过使用 Azure 机器学习来实现它。 最后，评估了关于 SAS 中可比较算法的原型解决方案的准确性和性能。  

 

## <a name="references"></a>参考
[1] 预测分析：不只是预测，W. McKnight，信息管理，2011 年 7/8 月，18-20 页。  

[2] Wikipedia article: [Accuracy and precision](http://en.wikipedia.org/wiki/Accuracy_and_precision)（Wikipedia 文章：准确性和精度）

[3] [CRISP-DM 1.0: Step-by-Step Data Mining Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)（CRISP-DM 1.0：分步数据挖掘指南）   

[4] [Big Data Marketing: Engage Your Customers More Effectively and Drive Value](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)（大数据市场营销：更有效地吸引客户并创造价值）

[5] [Azure AI 库](http://gallery.cortanaintelligence.com/) 中的 [电信流失模型模板](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) 
 

## <a name="appendix"></a>附录
![][10]

*图 12：关于流失原型的演示文稿的屏幕快照*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png

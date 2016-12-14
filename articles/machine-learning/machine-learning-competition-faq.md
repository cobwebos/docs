---
title: "Cortana Intelligence Competition 常见问题解答 | Microsoft Docs"
description: "有关 Microsoft Cortana Intelligence Competition 的常见问题。"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 9bac5154-a56c-4e78-9d67-34368b9d1624
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: haining;garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5beee424cb36bc1c0e1593003236f0675d07ae24


---
# <a name="microsoft-cortana-intelligence-competitions-faq"></a>Microsoft Cortana Intelligence Competition 常见问题
**什么是 Cortana Intelligence Competition？**

Microsoft 正在宣布举办 Cortana Intelligence Competition。 Cortana Intelligence Competition 要求数据爱好者们共同解决一些全世界最复杂的数据科学问题，进而组建全球性的团体。 Cortana Intelligence Competition 欢迎世界各地的数据爱好者参加竞赛，构建高精确和智能的数据科学模型。 我们举办的竞赛采用已经首次公开的唯一的数据集。 参与者将赢得奖励，或通过十佳公共排行榜得到认可。 请转到[此处](http://aka.ms/CIComp)访问竞赛主页。

**Microsoft 多久举办一次新竞赛？**

我们将举办第一次竞赛，Microsoft 定期举办竞赛，大约每隔 8 到 12 周举办一次。 

**在哪里可以咨询有关数据科学的一般问题？**

请访问我们的[Microsoft Azure 机器学习论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)。

**如何参赛？**

通过 Cortana Intelligence 库访问竞赛主页。 此页面包含所有正在进行的竞赛。 在每个竞争的注册页面上有对竞赛的详细说明、参与规则、奖品和竞赛时间。 请转到[此处](http://aka.ms/CIComp)访问竞赛主页。  

1. 在 Cortana Intelligence 库中查找想要参加的竞赛，阅读所有说明并观看教程视频，然后单击“参加竞赛”按钮以将初始试验复制到你现有的 Azure 机器学习工作区。 如果尚未访问工作区，必须提前创建一个。 运行初始试验，观察性能指标，然后使用你的创造力提高模型的性能。 在此步骤中可能会花费大部分时间。   
2. 在初始试验外，使用训练后的模型创建预测试验。 然后仔细调整 Web 服务的输入和输出架构，以确保它们符合竞赛文档中指定的要求。 教程文档通常详细说明了如何实现此操作。 如果可以，也可观看教程视频。   
3. 在预测试验外部署 Web 服务。 使用测试按钮或自动为你创建的 Excel 模板测试 Web 服务，以确保其正常运行。   
4. 作为竞赛项，提交你的 Web 服务，并在 Cortana Intelligence 库竞赛页面查看你的公共分数。 如果进入排行榜，那么恭喜你啦！  
   成功提交一项后，可以回到复制的初始试验，重复之前的工作，并更新预测试验、更新 Web 服务和提交新的竞赛项。   

**参加这些竞赛时，可以使用开放源代码工具吗？**

竞赛参与者利用 Azure 机器学习 Studio、Cortana Intelligence 套件中基于云的服务进行数据科学模型的开发，并创建提交的竞赛项。 机器学习工作室不只提供构造机器学习试验的 GUI 界面，还允许使用你自己的 R 和/或 Python 脚本进行本机执行。 机器学习工作室中的 R 和 Python 运行时附带有一组丰富的开放源代码 R/Python 包，也可作为试验的一部分导入自己的包。 机器学习工作室还具有内置的 JuPyteR Notebook 服务，为你执行自由式数据浏览。 当然，你始终可以下载竞赛中使用的数据集，并在机器学习工作室以外使用喜欢的工具浏览它。 

**是否只有数据科学家才能加入？**

否。 事实上，我们鼓励数据爱好者，对数据科学感兴趣的人和其他有志于数据的科学家参加我们的竞赛。 我们已设计了支持文档，以便每个人都可以参与到比赛中。 我们的目标受众是：

* 数据开发人员、数据科学家、BI 和专业分析师：负责生成数据和分析内容，供他人使用。
* 数据专员：了解数据的含义、用法和用途。
* 学生和研究人员：在大学期间或大规模开放在线课堂(MOOCs)参与过程中，通过学术程序学习和获取与技能相关的数据

**可以和同事组队参加吗？**

竞赛平台目前不支持团队参与。 每个竞赛项被绑定到单个用户 ID。 

**是否需要付费才能参加竞赛？**

无需付费便可参与竞赛。 但是，参与竞赛需要访问 Azure 机器学习工作区。 只需使用有效的 Microsoft 帐户或 Office 365 帐户登录，即可创建免费的工作区，无需使用信用卡。 如果你已经是 Azure 或 Cortana Intelligence 套件客户，可以在同一 Azure 订阅下创建并使用标准工作区。 如果想要购买 Azure 订阅，可以转到[此处](https://azure.microsoft.com/pricing)。 请注意使用标准工作区来构造试验时，将采用标准费率。 请在[此处](https://azure.microsoft.com/pricing/details/machine-learning/)参阅 Azure 机器学习的定价信息。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**公共分数和专用分数是什么？**

在大多数竞赛中，会注意到每次提交都会收到公共分数，通常在 10-20 分钟内。 但竞赛结束后，你将收到一个用于进行最终排名的专用分数。 下面是发生的具体情况：

* 按照分层，竞赛中使用的整个数据集被随机拆分为定型和测试（剩余）数据。 对随机拆分进行分层，以确保定型和测试数据中标签的分布一致。
* 将定型数据作为导入数据模块配置中初始试验的一部分上传并提供给你。
* 使用相同的分层，将测试数据进一步细分成公共和专用测试数据。
* 公共测试数据用于初始一轮评分。 结果被称为公共分数，这可在你提交竞赛项时的提交历史记录中看到。 此分数根据你提交的每个项计算。 使用此公共分数为你在公共排行榜上进行排序。
* 专用的测试数据用于竞赛结束后的最后一轮评分。 这称为专用分数。 
* 对于每个参与者，固定的编号可能有所不同，具体取决于竞赛，在你提交的项中，会自动选择具有最高公共分数的项，并将其输入为专用评分轮。 然后，具有最高专用得分的项被选择进入最终排名，据此最终确定获奖者。  

**在我们的平台上，客户可以举办竞赛吗？**

我们欢迎第三方组织与我们合作，在我们的平台上举办公共和专用竞赛。 我们的竞赛加入团队会很乐意与你探讨这类比赛的加入流程。  请通过 [compsupport@microsoft.com](mailto:compsupport@microsoft.com) 立即与我们联系，了解更多详细信息。 

**提交的限制是什么？**

典型的竞赛可能会选择限制24 小时内 (UTC 时间 00:00:00 到 23:59:59)你可以提交的项数、竞赛期间你可以提交的总项数。 超出限制时，将收到适当的错误消息。 

**如果我赢得比赛会怎样？**

Microsoft 将验证专用排行榜的结果，然后我们将与你联系。 请确保用户配置文件中你的电子邮件地址是最新的。

**如果赢得比赛，如何获得奖金？**

如果你是竞赛获胜者，则需要在资格、许可证和发布者的声明上签名。 此窗体将重述竞赛规则。 如果获胜者不是美国纳税人，则需要填写美国纳税表单 W-9 或 Form W-8BEN。 作为奖金支付过程的一部分，我们将使用获胜者的注册电子邮件，与所有获胜者联系。 请参考[条款和条件](http://aka.ms/comptermsandconditions)，了解其他详细信息。

**如果多个项收到相同的分数，怎么办？**

以提交时间作为辅助措施。 先提交的项优先于后提交的项。

**可以使用来宾工作区参与竞赛吗？**

否。 必须使用免费或标准工作区参与竞赛。 可以在来宾工作区中打开竞赛初始试验。 但是，不能创建提交的有效项。 

**可以在任何 Azure 区域中使用工作区参与竞赛吗？**

当前竞赛平台仅支持从美国中南部 Azure 区域的工作区提交竞赛项。 所有可用的工作区都在美国中南部。 但是，如果选择使用标准工作区，请确保位于美国中南部 Azure 区域。 否则提交将失败。 

**我们会保留用户的竞赛解决方案/项吗？**

仅保留用户项用于评估，以确定获胜的解决方案。 请参考[条款和条件](http://aka.ms/comptermsandconditions)，了解详细信息。




<!--HONumber=Nov16_HO3-->



---
title: 教程：合规性检查 - Azure 安全中心
description: 教程：了解如何使用 Azure 安全中心提高合规性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 1a6999c05c0b3dbaf572b376412f666c50c23df7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77604456"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教程：提高合规性
---

Azure 安全中心使用合规性仪表板，可以根据合规性要求简化相关过程  。 在仪表板中，安全中心会对你的 Azure 环境进行持续的评估，以便了解你的符合性情况。 安全中心根据安全最佳做法分析混合云环境中的风险因素。 这些评估会从支持的一组标准映射到符合性控件。 在合规性仪表板中，可以查看在特定的法规标准下，环境中所有评估的状态。 针对建议进行操作并减少环境中的风险因素以后，合规性情况得到了改善。

在本教程中，您将学习如何执行以下操作：

-   使用合规性仪表板评估合规性

-   针对建议进行操作，改进符合性情况

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要逐步执行本教程中介绍的功能，必须有安全中心的“标准”定价层。 可以免费试用安全中心标准版。
若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。 快速入门教程“[将 Azure 订阅载入到安全中心标准版](https://docs.microsoft.com/azure/security-center/security-center-get-started)”详细介绍了如何升级到标准版。

##  <a name="assess-your-regulatory-compliance"></a>评估合规性

安全中心会持续评估资源的配置以识别安全问题和漏洞。 这些评估以建议的形式提供，着重于改进安全机制。 在合规性仪表板中，可以查看一组符合性标准及其所有要求，其中的受支持的要求会映射到适用的安全评估。 这样你就可以根据这些评估的状态和标准来查看自己的符合性情况。

可以通过合规性仪表板视图重点了解你在符合某个重要的标准或规范方面存在哪些差距。 有了这个专注的视图，你还可以持续监视动态云环境和混合环境中一段时间内的符合性分数。

>[!NOTE]
> 默认情况下，安全中心支持以下法规标准：Azure CIS、PCI DSS 3.2、ISO 27001 和 SOC TSP。 
>
> 通过[动态合规性包（预览版）](update-regulatory-compliance-packages.md)功能，你可以将你的规章合规性仪表板中显示的标准升级到新的动态包  。 你还可以使用相同的预览功能来添加新的合规性包，并监视与其他标准的合规性。 

1.  在“安全中心”主菜单的“策略和符合性”下，选择“法规符合应”。   <br>
在屏幕顶部会显示一个仪表板，其中概述了你的符合性状态以及一组支持的符合性法规。 可以查看总体符合性分数，以及与每个标准相关联的已通过评估和失败的评估的数目。

    ![计算机说明高置信度](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  针对与自己相关的符合性标准，选择一个选项卡。 将会看到该标准的所有控件的列表。 对于适用的控件，可以查看与该控件相关联的已通过评估和失败的评估的详细信息。 某些控件为灰显状态。这些控件没有任何与之关联的安全中心评估。 你需要自行检查这些控件的要求，并在自己的环境中对其进行评估。 其中一部分可能与进程相关，与技术无关。

    ![符合性选项卡](./media/security-center-compliance-dashboard/compliance-pci.png)

1. 若要生成并下载总结当前特定标准的符合性状态的 PDF 报告，请单击“下载报告”  。

    该报告根据安全中心评估数据为所选标准提供符合性状态的高级别总结，并根据该特定标准的控制进行整理。 该报告可与相关利益干系人共享，并可用于为内部和外部审计员提供证据。

    ![下载](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>改进符合性情况

可以根据合规性仪表板中的信息直接在仪表板中采用相关建议，改进符合性情况。

1.  单击在仪表板中显示的失败评估即可查看该建议的详细信息。 每项建议都包含一组修正步骤，遵循这些步骤即可解决问题。

1.  可以选择特定的资源来查看更多的详细信息，然后解决与该资源的建议相关的问题。 <br>例如，在“Azure CIS 标准”选项卡中，  可以单击建议“需要安全地传输到存储帐户”。 

    ![符合性建议](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. 一路单击，直至出现建议信息，然后选择一个运行不正常的资源，此时就会直接转到在 Azure 门户中启用“安全的存储传输”这一体验。 

    有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

    ![符合性建议](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  在采取行动解决与建议相关的问题以后，就会在合规性仪表板报告中看到相关影响，因为你的符合性分数提高了。

    > [!NOTE]
    > 评估大约每 12 小时运行一次，因此只有在评估运行以后才能看到对符合性数据造成的影响。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用安全中心的合规性仪表板执行以下操作：

-   查看和监视与重要的标准和法规相对应的符合性情况。

-   改进符合性状态，方法是：解决相关的建议问题，观察符合性分数的改进情况。

合规性仪表板可以大大简化符合性过程，显著缩短为 Azure 和混合环境收集符合性证据所需的时间。

若要了解更多信息，请参阅以下文章：

-   [更新到规章合规性仪表板（预览版）中的动态合规性包](update-regulatory-compliance-packages.md) - 了解此预览功能，该功能允许你将规章合规性仪表板中显示的标准更新为新的动态包  。 你还可以使用相同的预览功能来添加新的合规性包，并监视与其他标准的合规性。 

-   [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。

-   [管理 Azure 安全中心安全建议](security-center-recommendations.md) - 了解如何使用 Azure 安全中心的建议来保护 Azure 资源。

-   [提高 Azure 安全中心的安全分数](security-center-secure-score.md) - 了解如何确定漏洞和安全建议的优先级，以便最大程度地改善安全状况。
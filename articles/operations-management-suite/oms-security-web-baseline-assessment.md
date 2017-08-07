---
title: "Operations Management Suite 安全和审核解决方案中的 Web 基线评估 | Microsoft Docs"
description: "本文档介绍如何使用 OMS 安全和审核解决方案中的 Web 基线评估执行所有被监视 Web 服务器的基线评估，以确保符合性和安全性。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 8be49b182df675fe3235d148b87379e1dff3a384
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 安全和审核解决方案中的 Web 基线评估
本文档有助于使用 OMS 安全和审核 Web 基线评估功能来评估被监视资源的安全状态。

## <a name="what-is-web-baseline-assessment"></a>什么是 Web 基线评估？
OMS 安全目前为操作系统提供安全基线评估。 它会每隔 24 小时扫描一次服务器的 OS 设置，帮助用户了解可能有漏洞的设置。 请阅读 [Operations Management Suite 安全和审核解决方案中的基线评估](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline)，详细了解此方面的情况。

Web 基线评估的目的是找出可能有漏洞的 Web 服务器设置。 Web 基线配置的三个主要源为：.NET、ASP.NET 和 IIS 配置。  就像操作系统基线评估一样，OMS 安全将每隔 24 小时扫描 Web 服务器一次，帮助用户了解其安全状态。  在 Internet Information Service (IIS) 中，配置的可自定义程度很高，因此可重写各种站点和应用程序级别。 除默认根级别之外，扫描程序还会检查每个应用程序/站点级别的设置。 这有助于确定可能有漏洞的设置，并快速进行补救，同时实施我们提供的针对这些设置的建议。


## <a name="web-security-baseline-assessment"></a>Web 安全基线评估

此预览版的该功能可以通过 OMS 的“搜索”选项和 OMS 的“安全和审核”仪表板来访问。 请按照以下步骤来执行相应的查询操作：

1. 在“Microsoft Operations Management Suite”主仪表板中，单击“安全和审核”磁贴。
2. 在“安全和审核”仪表板中，可以在 OS 基线视角旁边看到 Web 基线视角。
   
    ![OMS 安全和审核 Web 安全基线评估](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. 左窗格显示与基线相对比的 Web 服务器数、在所有进行评估的服务器上获得通过的规则的平均百分比，以及进行评估的服务器的列表。
4. 右窗格按“严重性”和“RuleType”列出那些失败的唯一规则。 单击任意一条右窗格规则会显示该规则的详细信息。 下图显示了一个示例。 评估的规则列在“规则设置”下。 “AzId”字段是每个规则的唯一标识符，供 Microsoft 用来跟踪基线规则。 除此之外，用户还可以看到“预期结果”（Microsoft 建议的值），以及有关该规则安全影响的其他详细信息。
    
    ![查询](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. 可以创建自己的查询来查看结果。 

可以使用的第一个查询是“Web 基线评估摘要”。 在“开始在此处搜索”字段中，键入以下查询：Type=SecurityBaselineSummary BaselineType=Web。 下面是一个输出示例：

![查询结果](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>在此查询中，每个记录表示单个服务器的评估摘要。

转到“日志搜索”中以后，即可键入不同的查询，获取有关 Web 基线评估的详细信息。 除了前面的查询，还可以使用此预览版中的以下查询：

**Web 基线规则评估**：每个记录表示单个服务器的单个 Web 基线规则评估。 它包括失败规则的所有数据、在其上评估规则的 SitePath、预期结果和实际结果。

查询：Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed

![查询结果 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

显示特定服务器的所有结果：此查询显示如何查看特定服务器的结果：查询：Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1

![查询结果 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

可使用这些记录/查询来创建自己的仪表板、报告或警报。 下面是一个 UI 控件示例，可以将其添加到仪表板中。 可以了解如何使用[此处](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)提供的 OMS 视图设计器来可视化数据。 以下屏幕以示例方式介绍了完成此自定义之后，磁贴的外观是什么样的。

![仪表板](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>另请参阅
本文档介绍了 OMS 安全和审核 Web 基线评估。 若要了解有关 OMS 安全的详细信息，请参阅以下文章：

* [Operations Management Suite (OMS) 概述](operations-management-suite-overview.md)
* [监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报](oms-security-responding-alerts.md)
* [监视 Operations Management Suite 安全和审核解决方案中的资源](oms-security-monitoring-resources.md)



---
title: "Operations Management Suite 安全和审核解决方案 Web 基线 | Microsoft Docs"
description: "本文档介绍如何使用 OMS 安全和审核解决方案执行所有被监视 Web 服务器的 Web 基线评估，以实现符合性和安全性。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 安全和审核解决方案中的 Web 基线评估
本文档有助于使用 [Operations Management Suite (OMS) 安全和审核解决方案](operations-management-suite-overview.md) Web 基线评估功能来评估被监视资源的安全状态。

## <a name="what-is-web-baseline-assessment"></a>什么是 Web 基线评估？
OMS 安全目前为操作系统提供安全基线评估。 它会每隔 24 小时扫描一次服务器的操作系统设置，帮助用户了解可能有漏洞的设置。 请阅读 [Operations Management Suite 安全和审核解决方案中的基线评估](oms-security-baseline.md)，详细了解此方面的情况。

Web 基线评估的目的是找出可能有漏洞的 Web 服务器设置。 Web 基线配置的三个主要源为：.NET、ASP.NET 和 IIS 配置。  就像操作系统基线评估一样，OMS 安全将每隔 24 小时扫描 Web 服务器一次，帮助用户了解其安全状态。  在 Internet Information Service (IIS) 中，配置的可自定义程度很高，因此可重写各种站点和应用程序级别。 除默认根级别之外，扫描程序还会检查每个应用程序/站点级别的设置。 这样有助于确定可能存在漏洞的设置的位置，快速进行修正。


## <a name="web-security-baseline-assessment"></a>Web 安全基线评估
就此预览版来说，该功能将使用“OMS 搜索”选项进行访问。 请按照以下步骤来执行相应的查询操作：

1. 在“Microsoft Operations Management Suite”主仪表板中，单击“安全和审核”磁贴。
2. 在“安全和审核”仪表板中，单击“日志搜索”按钮。
3. 可以使用的第一个查询是“Web 基线评估摘要”。 在“开始在此处搜索”字段中，键入以下查询：Type*=SecurityBaselineSummary BaselineType=web*。 以下屏幕是一个输出示例：

![Web 基线评估摘要](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> 在此查询中，每个记录表示单个服务器的评估摘要。

转到“日志搜索”中以后，即可键入不同的查询，获取有关 Web 基线评估的详细信息。 除了前面的查询，还可以使用此预览版中的以下查询。

**Web 基线规则评估**：每个记录表示单个服务器的单个 Web 基线规则评估。 它包括规则、位置、预期结果和实际结果的所有数据。

**查询**：Type*=SecurityBaseline BaselineType=web*

![Web 基线规则评估](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**显示特定服务器的所有结果**：词查询显示如何查看特定服务器的结果。

**查询**：*Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![所有结果](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

也可使用这些记录/查询来创建你自己的仪表板、报告或警报。 以下屏幕有一个 UI 控件示例，可以将其添加到仪表板中。 可以了解如何使用[此处](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)提供的 OMS 视图设计器来可视化数据。 以下屏幕以示例方式介绍了完成此自定义之后，磁贴的外观是什么样的。

![示例 UI](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> 如果想要了解为基线评估选中的设置，可以下载[此 Excel 电子表格](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690)，其中包含这些设置。

## <a name="see-also"></a>另请参阅
本文档介绍了 OMS 安全和审核 Web 基线评估。 若要了解有关 OMS 安全的详细信息，请参阅以下文章：

* [Operations Management Suite (OMS) 概述](operations-management-suite-overview.md)
* [监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报](oms-security-responding-alerts.md)
* [监视 Operations Management Suite 安全和审核解决方案中的资源](oms-security-monitoring-resources.md)



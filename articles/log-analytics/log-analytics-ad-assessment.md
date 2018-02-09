---
title: "使用 Azure Log Analytics 优化 Active Directory 环境 | Microsoft 文档"
description: "可以使用 Active Directory 运行状况检查解决方案定期评估环境的风险和运行状况。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a5e803cadfd08c42e12e6e34feee1c2d0d091d70
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>使用 Log Analytics 中的 Active Directory 运行状况检查解决方案优化 Active Directory 环境

![AD 运行状况检查符号](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

可以使用 Active Directory 运行状况检查解决方案定期评估服务器环境的风险和运行状况。 本文介绍如何安装和使用该解决方案，以针对潜在问题采取纠正措施。

此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。 该建议跨四个重点区域进行了分类，这将帮助你快速了解风险并采取措施。

建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 每一项建议都会就为何问题可能对你至关重要以及如何实施建议更改提供相关指导。

可以选择对组织最重要的重点领域，并跟踪一个运行正常无风险环境的进度。

在添加解决方案并完成检查后，会在环境中基础结构的“AD 运行状况检查”仪表板上显示有关重点领域的摘要信息。 以下部分介绍如何使用“AD 运行状况检查”仪表板上的信息，可以在其中查看并针对 Active Directory 服务器基础结构采取建议的操作。  

![AD 运行状况检查磁贴的图像](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![AD 运行状况检查仪表板的图像](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>先决条件

* Active Directory 运行状况检查解决方案要求在每台装有 Microsoft Monitoring Agent (MMA) 的计算机上安装受支持版本的 .NET Framework 4.5.2 或更高版本。  MMA 代理由 System Center 2016 - Operations Manager 和 Operations Manager 2012 R2 以及 Log Analytics 服务使用。 
* 该解决方案支持运行 Windows Server 2008 和 2008 R2、Windows Server 2012 和 2012 R2 以及 Windows Server 2016 的域控制器。
* 一个 Log Analytics 工作区，用于在 Azure 门户中通过 Azure Marketplace 添加 Active Directory 运行状况检查解决方案。  无需进一步的配置。

  > [!NOTE]
  > 添加该解决方案后，AdvisorAssessment.exe 文件会随代理添加到服务器中。 读取配置数据，然后将其发送到云中的 Log Analytics 服务进行处理。 逻辑应用于接收的数据，云服务记录数据。
  >
  >

若要对属于待评估域的域控制器执行运行状况检查，这些域控制器需要一个代理，并使用以下受支持的方法之一与 Log Analytics 建立连接：

1. 如果域控制器尚不受 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2 的监视，请安装 [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md)。
2. 如果域控制器受 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2 的监视并且管理组未与 Log Analytics 服务集成，则它可与 Log Analytics 共用多个宿主，以收集数据并将其转发到服务，同时仍可由 Operations Manager 监视。  
3. 否则，如果 Operations Manager 管理组已与服务集成，则在工作区中启用解决方案后，需要遵循[添加代理管理的计算机](log-analytics-om-agents.md#connecting-operations-manager-to-oms)中的步骤，为数据收集服务添加域控制器。  

域控制器上的代理向 Operations Manager 管理组报告、收集数据、将数据转发到为其分配的管理服务器，然后将数据从管理服务器直接发送到 Log Analytics 服务。  数据不会写入 Operations Manager 数据库。  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory 运行状况检查数据集合详细信息

Active Directory 运行状况检查使用已启用的代理收集以下来源的数据：

- 注册表 
- LDAP 
- .NET framework
- 事件日志 
- Active Directory 服务接口 (ADSI)
- Windows PowerShell
- 文件数据 
- Windows Management Instrumentation (WMI)
- DCDIAG 工具 API
- 文件复制服务 (NTFRS) API
- 自定义 C# 代码

数据从域控制器收集，并每隔七天转发到 Log Analytics。  

## <a name="understanding-how-recommendations-are-prioritized"></a>了解如何划分建议的优先级
每项建议都指定有一个权重值，用于标识该建议的相对重要性。 仅显示 10 个最重要的建议。

### <a name="how-weights-are-calculated"></a>如何计算权重
权重是基于三个关键因素的聚合值：

* 所发现的问题会导致不良后果的概率。 概率较高相当于建议的总体分数较高。
* 问题对组织的*影响*（如果它确实会导致不良后果）。 影响较大相当于建议的总体分数更高。
* 实施建议所需的*工作*。 工作量较大相当于建议的总体分数较低。

每一项建议的权重表示为可用于每个重点区域的总分百分比。 例如，如果安全性和合规性重点区域中建议的分数为 5%，则实施该建议将使总体安全性和合规性的分数增加 5%。

### <a name="focus-areas"></a>重点区域
**安全性和合规性** - 该重点区域显示针对潜在安全威胁和违规、企业策略，以及技术、法律和法规合规性要求的建议。

**可用性和业务连续性** - 该重点区域显示针对基础结构和业务保护的服务可用性、可恢复性建议。

**性能和可扩展性** - 该重点区域显示帮助组织实现 IT 基础结构扩展的建议，确保 IT 环境满足当前性能要求，并且能够应对不断变化的基础结构需求。

**升级、迁移和部署** - 该重点区域显示帮助升级、迁移并将 Active Directory 部署到现有基础结构的建议。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>每个重点区域的分数都应该以 100% 为目标吗？
不一定。 建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 但是，服务器基础结构各不相同，或多或少都需要一些具体的相关建议。 例如，如果虚拟机未公开到 Internet，那么一些安全建议可能没什么用。 某些可用性建议可能不太适用于提供低优先级临时数据收集和报告的服务。 对于成熟企业至关重要的问题对于一家初创公司可能不太重要。 可能要确定哪些重点区域是要优先考虑的，并查看分数随时间的变化。

每项建议都会提供有关该建议为何重要的指导。 考虑到 IT 服务的性质和组织的业务需求，应使用本指导来评估实施建议对你是否适用。

## <a name="use-health-check-focus-area-recommendations"></a>使用运行状况检查重点区域建议
安装该服务后，可以使用 Azure 门户中的解决方案页上的“运行状况检查”磁贴查看建议摘要。

查看概述的针对基础结构的合规性评估，并深入分析建议。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>查看针对重点区域的建议并采取纠正措施
3. 在 Azure 门户中的 Log Analytics 工作区单击“概述”磁贴。
4. 在“概述”页上，单击“Active Directory 运行状况检查”磁贴。 
5. 在“运行状况检查”页上，查看某个重点区域边栏选项卡中的摘要信息，并单击其中一个查看针对该重点区域的建议。
6. 在任何重点区域页上，均可以查看针对环境所做的优先级建议。 单击“**受影响的对象**”下的建议，以查看有关为何给出此建议的详细信息。<br><br> ![运行状况检查建议图像](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. 可以采取“建议的操作”中建议的纠正操作。 解决该项后，以后的评估将记录已执行的建议操作，并且合规性分数将提高。 已更正的项会显示为“通过的对象”。

## <a name="ignore-recommendations"></a>忽略建议
如果有要忽略的建议，可以创建 Log Analytics 用来防止建议出现在评估结果中的文本文件。

### <a name="to-identify-recommendations-that-you-will-ignore"></a>确定要忽略的建议
1. 在 Azure 门户中所选工作区对应的 Log Analytics 工作区页上，单击“日志搜索”磁贴。
2. 使用以下查询列出对于环境中计算机失败的建议。

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```
    >[!NOTE]
    > 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则上述查询会更改为如下所示。
    >
    > `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    下面是显示日志搜索查询的屏幕截图：<br><br> ![失败的建议](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. 选择要忽略的建议。 将 RecommendationId 的值用于接下来的过程。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>创建和使用 IgnoreRecommendations.txt 文本文件
1. 创建一个名为 IgnoreRecommendations.txt 的文件。
2. 在单独的行上粘贴或键入要 Log Analytics 忽略的每个建议的 RecommendationId，保存并关闭该文件。
3. 将以下文件夹中的文件置于每台要让 Log Analytics 忽略建议的计算机上。
   * 在具有 Microsoft Monitoring Agent（直接连接或通过 Operations Manager 连接）的计算机上 - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * 在 Operations Manager 2012 R2 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server 
   * 在 Operations Manager 2016 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>验证建议是否已被忽略
在下一次计划运行状况检查运行后（默认情况下每隔七天运行一次），指定的建议会被标记为“已忽略”，不会在仪表板上显示。

1. 可以使用以下日志搜索查询列出所有已忽略的建议。

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
    >[!NOTE]
    > 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则上述查询会更改为如下所示。
    >
    > `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. 如果以后决定想要查看已忽略的建议，请删除任何 IgnoreRecommendations.txt 文件，或者可以从中删除 RecommendationID。

## <a name="ad-health-check-solutions-faq"></a>AD 运行状况检查解决方案常见问题解答
运行状况检查的运行频率如何？

* 每隔七天运行检查。

是否有某种方法可配置运行状况检查的运行频率？

* 现在不行。

如果添加运行状况检查解决方案后发现另一台服务器，那么它是否会被检查？

* 是的，一经发现，会每隔七天对其进行一次检查。

如果服务器已停用，何时会将其从运行状况检查中删除？

* 如果服务器 3 周未提交数据，则会被删除。

*执行数据收集的进程的名称是什么？*

* AdvisorAssessment.exe

*收集数据需要多长时间？*

* 在服务器上执行的实际数据收集需要 1 个小时。 在有大量 Active Directory 服务器的服务器上，则可能需要更长时间。

*是否有某种方法来配置收集数据的时间？*

* 现在不行。

*仅显示前 10 条建议的原因*

* 我们并没有提供完整详尽的任务列表，而是建议先着重解决优先级较高的建议。 在解决这些建议后，其他建议将变为可用。 如果想要查看详细的列表，可以使用日志搜索查看所有建议。

*有没有方法来忽略建议？*

* 有的，请参阅上面的 [忽略建议](#ignore-recommendations) 部分。

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)了解如何分析详细的 AD 运行状况检查数据和建议。

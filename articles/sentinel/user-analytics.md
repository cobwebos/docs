---
title: 调查具有用户分析 Azure Sentinel 预览版中的用户 |Microsoft Docs
description: 了解有关如何调查中 Azure Sentinel 用户分析具有的用户。
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246315"
---
# <a name="investigate-users-with-user-analytics"></a>调查具有用户分析的用户

> [!IMPORTANT]
> Azure Sentinel 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

用户是你想要密切监视的实体。 为了帮助您深入了解你的用户，Azure Sentinel 与无缝集成 Azure 高级威胁防护。 此集成可以分析用户行为并确定哪些用户应调查首先，根据其，将发出警报和可疑活动模式在 Azure Sentinel 和 Microsoft 365 之间的优先级。

Azure Sentinel 丰富了用户使用从 Microsoft 365，以启用 Azure Active Directory 中的所有用户的全面的用户分析和风险分析的分析数据。 

## <a name="how-it-works"></a>工作原理

1. 根据您分析规则，匹配项时检测到，则 Azure Sentinel 将警报发送到 Azure ATP 的安全。
1. Azure ATP 检查哪些用户实体相关的警报，并计算为这些用户的调查优先级。
3. Azure ATP 后增加了分析规则中的数据的 Azure Sentinel 然后重新计算用户的分数。


## <a name="prerequisites"></a>必备组件

- Azure 高级威胁防护许可证 
- 若要启用此功能，需要在其中安装 Azure Sentinel 的租户的全局管理员权限

> [!NOTE]
> - 对于每个 Azure ATP 租户，可以连接 Azure Sentinel 的一个实例。
> - 用户分析目前仅适用于 Azure Active Directory 用户。 

## <a name="enable-user-analytics"></a>启用用户分析

1. 若要启用用户分析中 Azure Sentinel，在门户中转到**用户分析**页，然后单击**启用**。 这有关工作区中将信息发送到 Azure ATP 中。

1. 然后，它将转到 Azure ATP。 下**安全扩展插件**中**Microsoft Azure Sentinel**选项卡上，单击 **+ plus**添加，然后选择工作区。 
1. 单击“连接”。

## <a name="investigate-a-user"></a>调查用户

1. 在下面的 Azure Sentinel 菜单**用户分析**，查看根据其调查优先级的用户的列表。 评分基于 Azure Sentinel 警报和 Microsoft 365 警报。

2. 搜索你想要调查某个用户。 单击用户可访问用户页面。 查看用户的活动和警报，随着时间推移，时间线上。 您可以看到来自 Microsoft 365 和 Azure Sentinel 的所有活动。 也可以通过向下钻取从一种情况中的用户访问用户页面。
      
    ![用户](./media/user-analytics/user-investigation.png)

 
3. 为此来有效工作，必须正确设置[自定义警报规则](tutorial-detect-threats.md)映射到正确的用户标识符**帐户**实体。

    - 对于 Windows 事件，请将映射**帐户**到**SID**
    - Azure AD 数据 （这可以包括 Azure 活动的多个日志中找到） 或 Office 365 数据、 地图**帐户**属性设置为**GUID**，或**用户主体名称**。 

   ![Query](./media/user-analytics/query-window.png)



例如： 
> [!NOTE]
> 在活动日志中的 Azure 活动，调用方实体包括的 UPN。

1. 此查询搜索创建的资源或 Azure 活动日志中的部署活动的异常数。

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. 在自定义警报规则中，映射**帐户**属性设置为**调用方**。
   
   ![Query](./media/user-analytics/query-window.png)

3. 调查用户调查窗口中的用户。 有关如何调查用户的建议，请参阅[教程：调查用户](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user)。



## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何连接到 Azure Sentinel 威胁智能提供程序。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章。

- 若要开始使用 Azure Sentinel，需要 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/free/)。
- 了解如何[载入到 Azure Sentinel 数据](quickstart-onboard.md)，并[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。

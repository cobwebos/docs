---
title: "OMS 中的解决方案目标| Microsoft Docs"
description: "解决方案目标是 Operations Management Suite (OMS) 中的一项功能，允许将管理解决方案限制用于特定的一组代理。  本文介绍了如何创建范围配置并将其应用于解决方案。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: cb73a2d7ae57a5a11869259dbe913ae83ffb2b01
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-solution-targeting-in-operations-management-suite-oms-to-scope-management-solutions-to-specific-agents-preview"></a>使用 Operations Management Suite (OMS) 中的解决方案目标来将管理解决方案的应用范围限制为特定的代理（预览）
向 OMS 中添加解决方案时，默认情况下会自动将它部署到连接到 Log Analytics 的所有 Windows 和 Linux 代理。  你可能希望管理成本，并通过将解决方案的应用范围限制为特定的一组代理来限制为解决方案收集的数据量。  本文介绍了如何使用**解决方案目标**，这是一项 OMS 功能，允许向解决方案应用一个范围。

## <a name="how-to-target-a-solution"></a>如何为解决方案设定目标
可通过三个步骤为解决方案设定目标，如以下各节中所述。  注意，对于各个步骤，需要同时使用 OMS 门户和 Azure 门户。


### <a name="1-create-a-computer-group"></a>1.创建计算机组
通过在 Log Analytics 中创建[计算机组](../log-analytics/log-analytics-computer-groups.md)指定要在范围中包括的计算机。  计算机组可以基于日志搜索，也可以从其他来源（例如 Active Directory 或 WSUS 组）导入。 如[下文所述](#solutions-and-agents-that-cant-be-targeted)，只有直接连接到 Log Analytics 的计算机才会包括在范围中。

在工作区中创建计算机组后，将该组包括在范围配置中，然后可以将范围配置应用于一个或多个解决方案。
 
 
 ### <a name="2-create-a-scope-configuration"></a>2.创建范围配置
 **范围配置**包括一个或多个计算机组并且可以应用于一个或多个解决方案。 
 
 使用以下过程创建范围配置。  

 1. 在 Azure 门户中，导航到 **Log Analytics** 并选择工作区。
 2. 在工作区的属性中，在“工作区数据源”下选择“范围配置”。
 3. 单击“添加”以创建新的范围配置。
 4. 为范围配置键入**名称**。
 5. 单击“选择计算机组”。
 6. 选择你创建的计算机组和 （可选） 的任何其他组，将添加到配置。  单击“选择”。  
 6. 单击“确定”以创建范围配置。 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3.将范围配置应用于解决方案。
创建范围配置后，可以将其应用于一个或多个解决方案。  请注意，虽然单个范围配置可以用于多个解决方案，但每个解决方案只能使用一个范围配置。

使用以下过程应用范围配置。  

 1. 在 Azure 门户中，导航到 **Log Analytics** 并选择工作区。
 2. 在工作区的属性中，选择“解决方案”。
 3. 单击要为其设置应用范围的解决方案。
 4. 在解决方案的属性中，在“工作区数据源”下选择“解决方案目标”。  如果此选项不可用，则说明[无法为此解决方案设定目标](#solutions-and-agents-that-cant-be-targeted)。
 5. 单击“添加范围配置”。  如果已将某个配置应用于此解决方案，则此选项将不可用。  必须删除现有配置，才能添加另一个配置。
 6. 单击已创建的范围配置。
 7. 观察配置的“状态”以确保它显示为“成功”。  如果状态指出了错误，请单击配置右侧的省略号并选择“编辑范围配置”来进行更改。

## <a name="solutions-and-agents-that-cant-be-targeted"></a>无法设定目标的解决方案和代理
下面是无法用于解决方案目标的代理和解决方案满足的条件。

- 解决方案目标仅适用于部署到代理的解决方案。
- 解决方案目标仅适用于由 Microsoft 提供的解决方案。  它不适用于[由自己或合作伙伴创建的](operations-management-suite-solutions-creating.md)解决方案。
- 只能筛选出直接连接到 Log Analytics 的代理。  解决方案会自动部署到属于已连接的 Operations Manager 管理组的任何代理，无论它们是否包括在范围配置中。

### <a name="exceptions"></a>例外
解决方案目标不能用于以下解决方案，尽管它们满足上述条件。

- 代理运行状况评估

## <a name="next-steps"></a>后续步骤
- 参阅[将 Azure Log Analytics 管理解决方案添加到工作区](../log-analytics/log-analytics-add-solutions.md)，了解有关管理解决方案的详细信息，包括可以在环境中安装的解决方案。
- 参阅 [Log Analytics 日志搜索中的计算机组](../log-analytics/log-analytics-computer-groups.md)，了解有关创建计算机组的详细信息。
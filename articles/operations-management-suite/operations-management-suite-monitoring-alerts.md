---
title: "Microsoft 监视产品中的警报管理 | Microsoft 文档"
description: "警报指示需要管理员关注的某些问题。  本篇文章介绍在 System Center Operations Manager (SCOM) 和 Log Analytics 中创建和管理警报的差异，并且提供将两种产品用于混合警报管理策略的最佳做法。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>使用 Microsoft 监视管理警报
警报指示需要管理员关注的某些问题。  就警报创建方式、管理和分析方式以及通知已检测到重要问题的方式而言，Operations Management Suite (OMS) 中的 System Center Operations Manager (SCOM) 和 Log Analytics 差异明显。

## <a name="alerts-in-operations-manager"></a>Operations Manager 中的警报
SCOM 中的警报由个别规则或监视器生成，目的是指示特定问题。  监视器在进入错误状态时会生成警报，而规则可能生成的警报则指示与托管对象的运行状况没有直接关联的某些重要问题。  管理包包括各种为它们管理的应用程序或服务创建警报的工作流。  配置新管理包过程的一部分是对其进行微调，确保不会收到关于不严重问题的过多警报。

![SCOM 警报视图](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM 提供完整的警报管理系统，管理员在解决问题时可更改警报状态。  待问题解决后，管理员设置警报不再在显示活动警报的视图中显示的时间。  在监视器恢复正常状态时，从监视器中生成的警报会自动解除。

![警报状态](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Log Analytics 中的警报
Log Analytics 中的警报从自动定期运行的日志查询中创建。  可从任何日志查询中创建警报规则。  如果查询返回的结果与指定的条件相匹配，则创建警报。  这可能是一个特定查询，如果检测到特定事件则创建警报，或者可以使用更常规的查询来查找与特定应用程序相关的任何错误事件。

Log Analytics 警报作为事件写入 OMS 存储库，并且可通过日志查询检索。  它们没有与 SCOM 事件相似的状态，因此可指示问题解决的时间。

![OMS 警报](media/operations-management-suite-monitoring-alerts/oms-alert.png)

当 SCOM 用作 Log Analytics 的数据源时，SCOM 警报在创建和修改时会写入 OMS 存储库。  

![SCOM 警报](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[警报管理解决方案](http://technet.microsoft.com/library/mt484092.aspx)汇总了活动警报和若干常见查询以检索不同的警报集。  这使您更有效地分析你的警报比 SCOM 中的报表。  可深入研究此汇总信息，得到详细数据，并创建即席查询以检索不同的警报集。

![警报管理解决方案](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>通知
SCOM 中的通知向你发送邮件或文本以响应与特定条件匹配的警报。  可创建不同的通知订阅，根据以下条件通知不同用户：监视的对象、警报的严重性、检测到的问题类型或时间。

使用很少的订阅即可实现适用于大量管理包的完整通知策略。

![SCOM 警报](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

日志分析可以通知你通过邮件是否已通过对每个设置电子邮件通知操作创建警报[警报规则](http://technet.microsoft.com/library/mt614775.aspx)。  它无法像 SCOM 那样通过一条规则订阅多个警报。  还需要创建自己的警报规则，因为 OMS 不提供任何预配置的规则。

![Log Analytics 警报](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

无法在 Log Analytics 中全权管理 SCOM 警报，只可以在 Operations 控制台中修改它们。  Log Analytics 可用作警报管理过程的一部分，以提供 SCOM 自身没有的分析工具。

## <a name="alert-remediation"></a>警报修正
[修正](http://technet.microsoft.com/library/mt614775.aspx)是指尝试自动更正警报识别的问题。

SCOM 支持运行诊断和恢复，以响应进入不正常状态的监视器。  这与监视器创建警报同时发生。  诊断和恢复通常是作为在代理上运行的脚本来实现的。  诊断尝试收集关于检测到的问题的详细信息，恢复尝试更正问题。

Log Analytics 支持启动 [Azure 自动化 Runbook](https://azure.microsoft.com/documentation/services/automation/) 或调用 webhook 以响应 Log Analytics 警报。  Runbook 可包含在 PowerShell 中实现的复杂逻辑。  该脚本在 Azure 中运行，并且可以访问任何 Azure 资源或云提供的外部资源。  Azure 自动化可在本地数据中心的服务器上执行 Runbook，但在启动 Runbook 以响应 Log Analytics 警报时，此功能目前不可用。

SCOM 中的恢复和 OMS 中的 Runbook 均包含 PowerShell 脚本，但恢复更难以创建和管理，因为它们必须包含在管理包中。  Runbook 存储在 Azure 自动化中，它可以编写、测试和管理 Runbook。

如果将 SCOM 用作 Log Analytics 的数据源，则可通过日志查询创建 Log Analytics 警报，以检索存储在 OMS 存储库中的 SCOM 警报。  这将允许你以响应 SCOM 警报运行 Azure 自动化 runbook。  当然，由于 Runbook 在 Azure 中运行，所以这对恢复本地问题不是可行策略。

## <a name="next-steps"></a>后续步骤
* 在 [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx) 中了解警报的详细信息。


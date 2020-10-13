---
title: Azure 安全中心威胁智能报告 | Microsoft 文档
description: 此页面帮助你在调查过程中使用 Azure 安全中心威胁智能报告来查找有关安全警报的详细信息
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: f9b3fd0000a1b5dbba00995c37f96a89319de0e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440486"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure 安全中心威胁智能报告

本页介绍 Azure 安全中心的威胁智能报表如何帮助你了解有关触发安全警报的威胁的详细信息。


## <a name="what-is-a-threat-intelligence-report"></a>什么是威胁智能报告？

安全中心可以监视 Azure 资源、网络以及连接的合作伙伴解决方案中的安全信息，从而针对威胁进行保护。 分析该信息（通常需将多个来源的信息关联起来）即可确定威胁。 有关详细信息，请参阅 [Azure 安全中心如何检测和应对威胁](security-center-alerts-overview.md#detect-threats)。

当安全中心识别到威胁时，它将触发 [安全警报](security-center-managing-and-responding-alerts.md)，其中包含有关事件的详细信息，包括有关修正的建议。 为帮助事件响应团队调查和修正威胁，安全中心提供包含检测到的威胁相关信息的威胁智能报告。 该报表包含如下所示的信息：

* 攻击者的身份或关联项（如果此信息可用）
* 攻击者的目标
* 当前和历史攻击活动（如果此信息可用）
* 攻击者的策略、工具和过程
* 相关危害指标 (IoC)，例如 URL 和文件哈希
* 受害者研究，即研究行业和地理普遍性，帮助确定 Azure 资源有无风险
* 缓解计划和修复信息

> [!NOTE]
> 任何特定报表中的信息量都将有所不同；详细信息的级别基于恶意软件的活动和普遍性。

安全中心有三种类型的威胁报告，可因攻击而异。 可用报告有：

* **活动组报表**：向攻击者及其目标和战术提供深层深入。
* **活动报告**：重点提供特定攻击活动的详细信息。
* **威胁摘要报告**：包含前两个报告中的所有项目。

在事件响应过程中，这种类型的信息非常有用，在这种情况下，我们正在进行一项调查，以了解攻击来源、攻击者的动机以及将来可缓解此问题的方法。



## <a name="how-to-access-the-threat-intelligence-report"></a>如何访问威胁智能报告？

1. 从安全中心边栏打开 " **安全警报** " 页。
1. 选择警报。 
    此时将打开 "警报详细信息" 页，其中包含有关警报的更多详细信息。 下面是 **检测到的勒索软件指示器** 警报详细信息页。

    [![检测到的勒索软件指示器警报详细信息页](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. 选择指向报表的链接，PDF 将在默认浏览器中打开。

    [![可能不安全的操作警报详细信息页](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    你可以选择下载 PDF 报表。 

    >[!TIP]
    > 每个安全警报的可用信息量因警报类型而异。



## <a name="next-steps"></a>后续步骤

此页说明了如何在调查安全警报时打开威胁智能报表。 相关信息，请参阅以下页面：

* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理和响应安全警报。
* [处理 Azure 安全中心的安全事件](security-center-incident.md)
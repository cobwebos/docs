---
title: 通过 Azure 安全中心响应安全事件 | Microsoft 文档
description: 本文档介绍了如何利用 Azure 安全中心应对事件响应场景。
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 8af12f1c-4dce-4212-8ac4-170d4313492d
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: yurid
ms.openlocfilehash: 2f3a9344b9bd383ea59ff3cb400faa90942c5b9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-security-center-for-an-incident-response"></a>利用 Azure 安全中心进行事件响应
许多组织只有在遭受攻击后才会去学习如何响应安全事件。 为降低成本并减少破坏，必须在攻击发生之前制定事件响应计划。 可在事件响应的不同阶段使用 Azure 安全中心。

## <a name="incident-response-planning"></a>事件响应规划
计划是否有效取决于 3 大核心功能：可保护、检测和响应威胁。 保护是指防止事件发生，检测是指早期确定威胁，而响应则是指将攻击者逐出并还原系统，降低侵入的影响。

本文将使用 [Microsoft Azure 云端安全响应](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678)一文中的安全事件响应阶段，如下图所示：

![事件响应生命周期](./media/security-center-incident-response/security-center-incident-response-fig1.png)

可在检测、评估和诊断阶段运用安全中心。 以下示例介绍了安全中心如何在 3 个初始事件响应阶段发挥作用：

* **检测**：查看进行事件调查的第一个提示。
  * 示例：查看初始确认，明确在安全中心仪表板中引发了高优先级的安全警报。
* **评估**：进行初始评估，了解可疑活动的详细信息。
  * 示例：深入了解安全警报。
* **诊断**：进行技术性调查，确定抑制、缓解和解决威胁的各种策略。
  * 示例：按照安全中心在该特定安全警报中描述的补救步骤进行操作。

随后的方案说明了如何在安全事件的检测、评估和诊断/响应阶段利用安全中心。 在安全中心，[安全事件](security-center-incident.md)是对资源的所有警报汇总，与[网络攻击链](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/)模式保持一致。 事件显示在 [安全警报](security-center-managing-and-responding-alerts.md) 磁贴和边栏选项卡中。 事件显示相关警报列表，可使你获取有关每个匹配项的详细信息。 安全中心还提供单独的安全警报，其还可用于更正可疑活动。

## <a name="scenario"></a>场景
Contoso 最近向 Azure 迁移了部分本地资源，包括基于虚拟机的一些业务线工作负荷和 SQL 数据库。 目前，Contoso 的核心计算机安全事件响应团队 (CSIRT) 无法调查安全问题，因为其当前的事件响应工具尚未集成安全智能。 由于没有集成，检测阶段以及评估和诊断阶段都出现了问题（误报过多）。 在此次迁移过程中，他们决定加入安全中心计划，以便解决此问题。

在加载所有资源并遵循安全中心提供的所有安全建议进行操作以后，此迁移的第一阶段宣告完成。 Contoso CSIRT 负责处理计算机安全事件。 该团队由许多人员组成，负责处理安全事件。 团队成员都有明确的任务，确保在响应时不遗漏任何领域。

就本方案来说，需重点介绍 Contoso CSIRT 中以下成员的角色：

![事件响应生命周期](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Judy 负责安全操作。 其职责包括：

* 全天候监视和响应安全威胁。
* 必要时会问题上报给云工作负荷所有者或安全分析师。

Sam 是安全分析师，他的责任包括：

* 调查各种攻击。
* 根据警报进行补救。
* 与工作负荷所有者合作，确定并应用补救措施。

正如所见，Judy 和 Sam 的职责不同，他们必须通力合作，共享安全中心信息。

## <a name="recommended-solution"></a>建议的解决方案
由于 Judy 和 Sam 的角色不同，他们需要在安全中心的不同领域获取日常活动的相关信息。 Judy 需要在日常监视时使用**安全警报**。

![安全警报](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Judy 需要在检测和评估阶段使用安全警报。 完成初步评估后，如需额外调查，Judy 可能会将问题上报给 Sam。 此时，Sam 会利用安全中心提供的信息，有时结合其他数据源，进入到诊断阶段。

## <a name="how-to-implement-this-solution"></a>如何实施该解决方案
若要了解如何在事件响应方案中使用 Azure 安全中心，请遵循 Judy 在“检测和评估”阶段的步骤，然后了解 Sam 如何诊断问题。

### <a name="detect-and-assess-incident-response-stages"></a>检测和评估事件响应阶段
Judy 登录到 Azure 门户，现在位于安全中心控制台中。 她开始执行以下步骤的操作，按日常监视惯例查看优先级高的安全警报：

1. 单击“安全警报”磁贴，再访问“安全警报”边栏选项卡。
    ![“安全警报”边栏选项卡](./media/security-center-incident-response/security-center-incident-response-fig4.png)

   > [!NOTE]
   > 就本方案来说，Judy 需要评估“恶意 SQL 活动”警报，如上图所示。
   >
   >
2. 单击“恶意 SQL 活动”警报，在“恶意 SQL 活动”边栏选项卡中查看受攻击的资源：![事件详细信息](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    Judy 可查看该边栏选项卡，记下受攻击的资源、此攻击发生的次数和检测到它的时间。
3. 单击“受攻击的资源”，获取有关该攻击的详细信息。

读完描述以后，Judy 确信这不是误报，因此应将此案例上报给 Sam。

### <a name="diagnose-incident-response-stage"></a>事件响应的诊断阶段
Sam 收到 Judy 的案例，开始查看安全中心建议的补救步骤。

![事件响应生命周期](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>其他资源
如果公司在调查过程中使用安全信息和事件管理 (SIEM) 解决方案，则其还可[将安全中心与解决方案集成](security-center-integrating-alerts-with-log-integration.md)。 也可通过 [Azure 日志集成工具](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/)集成 Azure 审核日志和虚拟机 (VM) 安全事件。 可结合使用该信息和安全中心提供的信息对攻击进行调查。 也可使用安全中心的[调查](https://docs.microsoft.com/azure/security-center/security-center-investigation)功能来确定事件的根本原因。

## <a name="conclusion"></a>结束语
在事件发生之前组建安全团队对组织来说非常重要，这会积极地影响事件的处理方式。 必须使用正确的工具监视相关资源，这样该团队才能准确应对安全事件。 安全中心[检测功能](security-center-detection-capabilities.md)可协助 IT 部门快速响应安全事件，并就安全问题提供补救措施。

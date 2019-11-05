---
title: 使用安全策略 | Microsoft Docs
description: 本文介绍如何使用 Azure 安全中心的安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4ac6ac52d6d950d814a37e94ea2801c2ba8e4170
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521211"
---
# <a name="working-with-security-policies"></a>使用安全策略

本文介绍如何配置安全策略，以及如何在安全中心查看这些策略。 

## <a name="introduction-to-security-policies"></a>安全策略简介

安全策略定义工作负荷的所需配置，并有助于确保符合公司或机构的安全要求。

Azure 安全中心根据所选的策略，进行安全建议。 安全中心策略基于 Azure 策略中创建的策略计划。 可以使用[Azure 策略](../governance/policy/overview.md)管理策略，并跨管理组和多个订阅设置策略。

安全中心为使用安全策略提供以下选项：

* **查看和编辑内置默认策略**-启用安全中心时，名为 "ASC 默认值" 的内置计划会自动分配到所有安全中心注册的订阅（免费或标准层）。 若要自定义此计划，你可以在其中启用或禁用单个策略。 请参阅[内置安全策略](security-center-policy-definitions.md)列表，了解现成可用的选项。

* **添加你自己的自定义策略**-如果想要自定义适用于你的订阅的安全计划，可以在安全中心执行此操作。 如果你的计算机不遵循你所创建的策略，则会收到建议。 有关生成和分配自定义策略的说明，请参阅[使用自定义安全策略](custom-security-policies.md)。

* **添加法规符合性策略**-安全中心的符合性仪表板显示了环境中特定标准或法规（如 Azure CIS、NIST SP 800-53 R4、SWIFT CSP）的所有评估状态CSCF-v2020). 有关详细信息，请参阅[提升法规符合性](security-center-compliance-dashboard.md)。


## <a name="managing-your-security-policies"></a>管理安全策略

要在安全中心内查看安全策略，请执行以下操作：

1. 在“安全中心”仪表板中，选择“安全策略”。

    ![“策略管理”窗格](./media/security-center-policies/security-center-policy-mgt.png)

   在“策略管理”屏幕中，可以看到管理组数、订阅数、工作区数以及管理组结构。

1. 选择想要查看其策略的订阅或管理组。

1. 此时将显示该订阅或管理组的 "安全策略" 页。 它显示可用和已分配的策略。

   ![策略屏幕](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 如果默认策略旁边有一个标签 "MG 继承"，则表示该策略已分配给管理组，并由正在查看的订阅继承。


1. 从此页上的可用选项中进行选择：

    1. 若要使用行业策略，请单击 "**添加更多标准**"。 有关详细信息，请参阅[更新为动态符合性包](update-regulatory-compliance-packages.md)。

    1. 若要分配和管理自定义计划，请单击 "**添加自定义计划**"。 有关详细信息，请参阅[使用自定义安全策略](custom-security-policies.md)。

    1. 若要查看和编辑默认策略，请单击 "**查看有效策略**" 并继续，如下所述。 

       ![策略屏幕](./media/security-center-policies/policy-screen.png)
       
       此**安全策略**屏幕反映了在所选的订阅或管理组上分配的策略执行的操作。
       
       * 使用顶部的链接打开应用于订阅或管理组的策略**分配**。 这些链接使你可以访问分配并编辑或禁用策略。 例如，如果发现特定策略分配有效地拒绝 endpoint protection，请使用链接编辑或禁用策略。
       
       * 在策略列表中，可以看到策略有效应用于订阅或管理组。 将考虑适用于作用域的每个策略的设置，并显示策略采取的操作的累计结果。 例如，如果在一个策略分配中已禁用，但在另一个设置为 AuditIfNotExist 的情况下，则累积影响将应用 AuditIfNotExist。 更积极的效果始终优先。
       
       * 策略效果可以是：追加、审核、AuditIfNotExists、拒绝、DeployIfNotExists 和禁用。 有关如何应用效果的详细信息，请参阅[了解策略效果](../governance/policy/concepts/effects.md)。

       > [!NOTE]
       > 查看已分配的策略时，可以看到多个分配并且可以看到每个分配如何自行配置。


## <a name="who-can-edit-security-policies"></a>谁可以编辑安全策略？

你可以在 Azure Policy 门户中通过 REST API 或 Windows PowerShell 编辑安全策略。

安全中心使用基于角色的访问控制 (RBAC)，提供可以分配给 Azure 中用户、组和服务的内置角色。 用户打开安全中心时，只能看到其有权访问的资源的相关信息。 这意味着向用户分配了对资源的订阅的*所有者*、*参与者*或*读者*角色。 此外，还提供了两个特定的安全中心角色：

- **安全读取者**：有权查看安全中心，包括建议、警报、策略和运行状况，但无法进行更改。
- **安全管理员**：拥有与安全读取者相同的查看权限，不同之处在于还有权更新安全策略、拒绝建议和关闭警报。


## <a name="disable-security-policies"></a>禁用安全策略
如果默认安全策略正在生成与你的环境不相关的建议，则可以通过禁用发送建议的策略定义来停止它。
有关建议的详细信息，请参阅[管理安全建议](security-center-recommendations.md)。

1. 在安全中心的“策略和符合性”部分中，单击“安全策略”。

   ![策略管理](./media/tutorial-security-policy/policy-management.png)

2. 单击要禁用其建议的订阅或管理组。

   > [!NOTE]
   > 请记住，管理组将其策略应用于其订阅。 因此，如果禁用订阅的策略，并且订阅属于仍使用相同策略的管理组，则你将继续收到策略建议。 仍将从管理级别应用该策略，且仍将生成建议。

1. 单击分配的策略。

   ![禁用策略](./media/tutorial-security-policy/security-policy.png)

1. 在“参数”部分中，搜索调用要禁用的建议的策略，然后从下拉列表中选择“禁用”

   ![禁用策略](./media/tutorial-security-policy/disable-policy.png)

1. 单击“保存”。

   > [!NOTE]
   > 禁用策略更改可能需要长达 12 小时才会生效。



## <a name="next-steps"></a>后续步骤
本文介绍了安全策略。 有关相关信息，请参阅以下文章：

* 有关如何使用 PowerShell 设置策略的说明，请参阅[快速入门：使用 Azure PowerShell 模块创建策略分配以识别不符合的资源](../governance/policy/assign-policy-powershell.md)

* 有关如何在 Azure 策略中编辑安全策略的说明，请参阅[创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)。

* 有关如何使用 Azure 策略跨订阅或管理组设置策略的说明，请参阅[什么是 Azure 策略？](../governance/policy/overview.md)
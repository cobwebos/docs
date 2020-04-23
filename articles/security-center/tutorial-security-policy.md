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
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024874"
---
# <a name="working-with-security-policies"></a>使用安全策略

本文介绍如何配置安全策略，以及如何在安全中心查看这些策略。 

## <a name="introduction-to-security-policies"></a>安全策略简介

安全策略定义工作负荷的所需配置，并帮助确保遵守公司或监管机构的安全要求。

Azure 安全中心根据所选的策略提供安全建议。 安全中心策略基于 Azure Policy 中创建的策略计划。 可以使用 [Azure Policy](../governance/policy/overview.md) 来管理策略，以及跨管理组和多个订阅设置策略。

安全中心提供以下选项来让用户使用安全策略：

* **查看和编辑内置默认策略** - 启用安全中心时，会将一个名为“ASC default”的内置计划自动分配到所有已在安全中心注册的订阅（免费或标准层）。 若要自定义此计划，可在其中启用或禁用单个策略。 请参阅[内置安全策略](security-center-policy-definitions.md)列表，了解现成的可用选项。

* **添加自己的自定义策略** - 如果希望自定义要应用到自己的订阅的安全计划，可以在安全中心执行此操作。 如果计算机不遵循创建的策略，则你会收到建议。 有关构建和分配自定义策略的说明，请参阅[使用自定义安全策略](custom-security-policies.md)。

* **添加合规性策略** - 安全中心的合规性仪表板显示环境内的所有评估在特定标准或法规（例如 Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020）上下文中的状态。 有关详细信息，请参阅[改善合规性](security-center-compliance-dashboard.md)。


## <a name="managing-your-security-policies"></a>管理安全策略

要在安全中心内查看安全策略，请执行以下操作：

1. 在“安全中心”仪表板中，选择“安全策略”********。

    ![“策略管理”窗格](./media/security-center-policies/security-center-policy-mgt.png)

   在“策略管理”屏幕中，可以看到管理组数、订阅数、工作区数以及管理组结构****。

1. 选择想要查看其策略的订阅或管理组。

1. 此时会显示该订阅或管理组的安全策略页。 其中显示了可用和已分配的策略。

   ![策略屏幕](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 如果默认策略旁边有一个标签“MG 已继承”，则表示该策略已分配到某个管理组，并已由当前你正在查看的订阅继承。


1. 从此页提供的可用选项中进行选择：

    1. 要配合产业政策，请选择"**添加更多标准**"。 有关详细信息，请参阅[更新到动态合规性包](update-regulatory-compliance-packages.md)。

    1. 要分配和管理自定义计划，请选择"**添加自定义计划**"。 有关详细信息，请参阅[使用自定义安全策略](custom-security-policies.md)。

    1. 要查看和编辑默认策略，请选择 **"查看有效策略**"，然后按照如下所述继续操作。 

       ![策略屏幕](./media/security-center-policies/policy-screen.png)
       
       此“安全策略”屏幕反映在所选订阅或管理组中分配的策略所执行的操作。****
       
       * 使用顶部的链接打开在订阅或管理组中应用的每个策略**分配**。 可以使用这些链接访问分配，以及编辑或禁用策略。 例如，如果你发现特定的策略分配正在有效地拒绝终结点保护，可使用该链接来编辑或禁用该策略。
       
       * 在策略列表中，可以看到策略有效应用于订阅或管理组。 将考虑适用于该范围的每个策略的设置，并显示该策略执行的操作的累积效果。 例如，如果一个分配禁用此策略，而另一个设置为 AuditIfNotExist，则累积效果将应用 AuditIfNotExist。 更积极的效果始终优先。
       
       * 策略效果可以是：追加、审核、AuditIfNotExists、拒绝、DeployIfNotExists 和禁用。 有关如何应用效果的详细信息，请参阅[了解策略效果](../governance/policy/concepts/effects.md)。

       > [!NOTE]
       > 查看已分配的策略时，可以看到多个分配并且可以看到每个分配如何自行配置。


## <a name="who-can-edit-security-policies"></a>谁可以编辑安全策略？

你可以在 Azure Policy 门户中通过 REST API 或 Windows PowerShell 编辑安全策略。

安全中心使用基于角色的访问控制 (RBAC)，提供可以分配给 Azure 中用户、组和服务的内置角色。 用户打开安全中心时，只能看到其有权访问的资源的相关信息。 这意味着，将为用户分配对资源订阅的所有者、参与者或读取者角色。****** 除这些角色外，还有两个特定的安全中心角色：

- **安全读取器**：具有安全中心的访问权限，其中包括建议、警报、策略和运行状况，但不能进行更改。
- **安全管理员**：具有与*安全读取器*相同的视图权限，还可以更新安全策略并取消建议和警报。


## <a name="disable-security-policies"></a>禁用安全策略
如果默认安全策略生成的建议与你的环境不相关，可以通过禁用发送建议的策略定义将其停止。
有关建议的详细信息，请参阅[管理安全建议](security-center-recommendations.md)。

1. 在安全中心中，从**策略&合规性**部分中，选择**安全策略**。

   ![策略管理](./media/tutorial-security-policy/policy-management.png)

2. 选择要为其禁用建议的订阅或管理组。

   > [!NOTE]
   > 请记住，管理组将其策略应用于其订阅。 因此，如果禁用订阅的策略，并且订阅属于仍使用相同策略的管理组，则你将继续收到策略建议。 仍将从管理级别应用该策略，且仍将生成建议。

1. 选择 **"查看有效的策略**"。

   ![禁用策略](./media/tutorial-security-policy/view-effective-policy.png)

1. 选择分配的策略。

   ![禁用策略](./media/tutorial-security-policy/security-policy.png)

1. 在“参数”**** 部分中，搜索调用要禁用的建议的策略，然后从下拉列表中选择“禁用”****

   ![禁用策略](./media/tutorial-security-policy/disable-policy.png)

1. 选择“保存”。 

   > [!NOTE]
   > 禁用策略更改可能需要长达 12 小时才会生效。



## <a name="next-steps"></a>后续步骤
本文介绍了安全策略。 有关更多信息，请参阅以下文章：

* 有关如何使用 PowerShell 设置策略的说明，请参阅[快速入门：创建策略分配，使用 Azure PowerShell 模块标识不合规资源](../governance/policy/assign-policy-powershell.md)

* 有关如何在 Azure Policy 中编辑安全策略的说明，请参阅[创建和管理策略以强制实施合规性](../governance/policy/tutorials/create-and-manage.md)。

* 有关如何使用 Azure Policy 跨订阅或针对管理组设置策略的说明，请参阅[什么是 Azure Policy？](../governance/policy/overview.md)
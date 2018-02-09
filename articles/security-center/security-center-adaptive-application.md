---
title: "Azure 安全中心的自适应应用程序控制 | Microsoft Docs"
description: "本文档介绍如何在 Azure 安全中心使用自适应应用程序控制将在 Azure VM 中运行的应用程序加入允许列表。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: yurid
ms.openlocfilehash: b1c30b6a0d768f04608c4e46a2e29d20f566964a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Azure 安全中心（预览版）的自适应应用程序控制
了解如何通过本演练在 Azure 安全中心配置应用程序控制。

## <a name="what-are-adaptive-application-controls-in-security-center"></a>安全中心的自适应应用程序控制是什么？
可以通过自适应应用程序控制来控制哪些应用程序能够在位于 Azure 中的 VM 上运行，这样有很多好处，其中之一是能够增强 VM 对恶意软件的抵抗力。 安全中心使用机器学习来分析在 VM 中运行的进程，帮助你运用此智能来应用允许列表规则。 此功能大大简化配置和维护应用程序允许列表的过程，让你可以：

- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。
- 遵循组织要求只能使用许可软件的安全策略。
- 避免在环境中使用不需要的软件。
- 避免运行旧的不受支持的应用。
- 防止使用组织不允许的特定软件工具。
- 允许 IT 部门控制用户使用应用来访问敏感数据。

## <a name="how-to-enable-adaptive-application-controls"></a>如何启用自适应应用程序控制？
可以通过自适应应用程序控制来定义一组应用程序，允许这些应用程序在配置的资源组上运行。 此功能仅适用于 Windows 计算机（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）。 以下步骤可以用来在安全中心配置应用程序允许列表功能：

1. 打开“安全中心”仪表板。
2. 在左窗格的“高级云防御”下选择“自适应应用程序控制”。

    ![防御](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

此时会显示“自适应应用程序控件”页。

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

“资源组”部分包含三个选项卡：

* 已配置：所含 VM 已配置了应用程序控制的资源组的列表。
* 已建议：建议对其实施应用程序控制的资源组的列表。 安全中心使用机器学习，根据 VM 是否一致地运行相同应用程序来确定适用于应用程序控制的 VM。
* 无建议：所含 VM 没有任何应用程序控制建议的资源组的列表。 例如，其上的应用程序始终变化，尚未达到稳定状态的 VM。

### <a name="configure-a-new-application-control-policy"></a>配置新的应用程序控制策略
1. 单击“已建议”选项卡会出现一个列表，其中包含的资源组带有应用程序控制建议：

  ![建议](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  此列表包括：

  - 名称：订阅和资源组的名称
  - VM 数：资源组中虚拟机的数目
  - 状态：建议的状态，大多数情况下为“开放”
  - 严重性：建议的严重性级别

2. 选择一个资源组，打开“创建应用程序控制规则”选项。

  ![应用程序控制规则](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. 在“选择 VM”中查看建议的 VM 的列表，取消选中不需向其应用应用程序控制的 VM。 在“选择适用于允许列表规则的进程”中查看建议的应用程序的列表，取消选中不需向其应用应用程序控制的应用程序。 此列表包括：

  - 名称：完整的应用程序路径
  - 进程数：每个路径中驻留的应用程序数
  - **常用**：“是”表示这些进程已在此资源组的大多数 VM 上执行。
  - **可利用**：警告图标表示攻击者可能会利用应用程序来规避应用程序允许列表。 建议在这些应用程序审批前查看它们。
  - **用户**：允许运行应用程序的用户

4. 选择完以后，请选择“创建”。

安全中心始终默认在“审核”模式下启用应用程序控制。 在验证允许列表对工作负荷没有任何负面影响之后，即可更改为“强制”模式。

安全中心需要至少两周的数据才能创建基线并根据 VM 组填充唯一建议。 安全中心标准层的新客户会遇到一种行为，即 VM 组首先显示在“无建议”选项卡下。

> [!NOTE]
> 安全中心始终会遵循安全方面的最佳做法，尝试为应该加入允许列表的应用程序创建发布者规则，只有在应用程序没有发布者信息（即未签名）的情况下，才会为特定 EXE 的完整路径创建路径规则。
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>编辑和监视配置了应用程序控制的组

1. 若要编辑和监视使用应用程序控制配置的组，请返回到“自适应应用程序控制”页，在“资源组”下选择“已配置”：

  ![资源组](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  此列表包括：

  - 名称：订阅和资源组的名称
  - VM 数：资源组中虚拟机的数目
  - 模式：“审核”模式会记录运行未加入允许列表的应用程序的尝试；“阻止”模式会阻止未加入允许列表的应用程序运行
  - **问题**：任何当前的冲突

2. 选择一个资源组，在“编辑应用程序控制策略”页中进行更改。

  ![保护](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. 在“保护模式”下，可以在以下选项之间进行选择：

  - **审核**：在此模式下，应用程序控制解决方案不强制实施规则，只审核受保护 VM 上的活动。 如果在阻止应用在目标 VM 中运行之前需先观察总体行为，建议使用此模式。
  - **强制**：在此模式下，应用程序控制解决方案强制实施规则，确保阻止不允许运行的应用程序。

  如前所述，默认情况下，新的应用程序控制策略始终在“审核”模式下配置。 可以在“策略扩展”下添加自己的需要加入允许列表的应用程序路径。 添加这些路径以后，安全中心会在已有规则的基础上为这些应用程序创建适当的规则。

  在“最新问题”部分会列出任何当前的冲突。

  ![问题](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  这些应用程序包括：
  - 问题：已记录的任何冲突，可能包括：

      - ViolationsBlocked：当解决方案启用“强制”模式时，尝试执行未加入允许列表的应用程序所出现的冲突。
      - ViolationsAudited：当解决方案启用“审核”模式时，执行未加入允许列表的应用程序所出现的冲突。
      - RulesViolatedManually：当用户尝试在 VM 上手动配置规则（而不是通过 ASC 管理门户进行配置）时出现的冲突。

 - VM 和数：有此类问题的虚拟机数。

  单击每一行都会重定向到 [Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)页，其中显示具有此类冲突的所有 VM 的相关信息。 如果单击每行末尾的三点符号，则可删除该特定条目。 “已配置虚拟机”部分列出了应用这些规则的 VM。

  ![已配置虚拟机](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  在“发布服务器允许列表规则”下，列表包含：

  - **规则**：根据证书信息为其创建发布服务器规则的应用程序，这些信息是为每个应用程序找到的
  - **用户数**：允许运行每个应用程序的用户数

  有关详细信息，请参阅 [Understanding Publisher Rules in Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker)（了解 Applocker 中的发布者规则）。

  ![允许列表规则](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  如果单击每行末尾的三点符号，则可删除该特定规则或编辑允许的用户。

  “路径允许列表规则”部分列出了整个应用程序路径（包括可执行文件），该路径适用于未使用数字证书进行签名但目前仍在允许列表规则中的应用程序。

  > [!NOTE]
  > 默认情况下，安全中心始终会遵循安全方面的最佳做法，尝试为应该加入允许列表的 EXE 创建发布者规则，只有在 EXE 没有发布者信息（即未签名）的情况下，才会为特定 EXE 的完整路径创建路径规则。

  ![路径允许列表规则](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  此列表包含：
  - 名称：可执行文件的完整路径
  - **用户数**：允许运行每个应用程序的用户数

  如果单击每行末尾的三点符号，则可删除该特定规则或编辑允许的用户。

4. 在“自适应应用程序控制”页进行更改以后，单击“保存”按钮。 如果决定不应用所做的更改，请单击“放弃”。

### <a name="not-recommended-list"></a>“不建议”列表

安全中心只为运行稳定的一组应用程序的虚拟机建议应用程序允许列表。 如果已关联 VM 上的应用程序始终变化不定，则不会创建建议。

![建议](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

此列表包含：
- 名称：订阅和资源组的名称
- VM 数：资源组中虚拟机的数目

## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure 安全中心使用自适应应用程序控制将在 Azure VM 中运行的应用程序加入允许列表。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Managing and responding to security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

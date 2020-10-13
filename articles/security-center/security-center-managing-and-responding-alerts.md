---
title: 在 Azure 安全中心管理安全警报 | Microsoft 文档
description: 本文档旨在帮助使用 Azure 安全中心功能来管理和响应安全警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e17c7d6618d67d66c89875696c5c529af3a85ea9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440553"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>在 Azure 安全中心管理和响应安全警报

本主题介绍如何查看和处理收到的警报，以保护资源。 

* 若要了解不同类型的警报，请参阅[安全警报类型](alerts-reference.md)。
* 有关安全中心如何生成警报的概述，请参阅 [Azure 安全中心如何检测和应对威胁](security-center-alerts-overview.md)。

> [!NOTE]
> 若要启用高级检测，请启用 Azure Defender。 有免费试用版可用。 要升级，请选择 [安全策略](tutorial-security-policy.md)中的“定价层”。 请参阅 [Azure 安全中心定价](security-center-pricing.md)，了解详细信息。

## <a name="what-are-security-alerts"></a>什么是安全警报？
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。

> [!NOTE]
> 有关安全中心检测功能工作原理的详细信息，请参阅 [Azure 安全中心如何检测和应对威胁](security-center-alerts-overview.md#detect-threats)。

## <a name="manage-your-security-alerts"></a>管理安全警报

1. 在安全中心仪表板中，参阅“威胁防护”磁贴以查看和概要了解警报。

    ![安全中心的“安全警报”磁贴](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 若要查看有关警报的更多详细信息，请单击该磁贴。

   ![安全中心的“安全警报”](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 若要筛选显示的警报，请单击“筛选”，然后从打开的“筛选器”边栏选项卡中选择要应用的筛选器选项。 列表会根据所选筛选器进行更新。 筛选功能可能非常有用。 例如，假设正在调查系统中的潜在危害，需要处理过去 24 小时内发生的安全警报。

    ![筛选安全中心的警报](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>响应安全警报

1. 在“安全警报”列表中，单击一个安全警报。 此时会显示所涉及的资源以及对抗攻击所需执行的步骤。

    ![响应安全警报](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 查看此信息后，请单击受攻击的资源。

    "安全警报" 页的左窗格显示有关安全警报的高级信息： "标题"、"严重性"、"状态"、"活动时间"、"可疑活动的描述" 和受影响的资源。 与受影响的资源一起，Azure 标记与资源相关。 当调查警报时，使用这些资源来推断资源的组织上下文。

    右窗格包含 " **警报详细信息** " 选项卡，其中包含警报的更多详细信息，以帮助你调查问题： IP 地址、文件、进程等。
     
    ![有关如何处理安全警报的建议](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    同样，在右窗格中是 " **采取操作** " 选项卡。使用此选项卡执行有关安全警报的其他操作。 操作，例如：
    - *缓解威胁* -提供此安全警报的手动修正步骤
    - *防止未来攻击* -提供安全建议，以帮助减少攻击面，提高安全状况，并防止未来攻击
    - *触发自动响应* -提供触发逻辑应用作为对此安全警报的响应的选项
    - *禁止显示类似警报* -如果警报与组织无关，则提供禁止显示具有类似特征的未来警报的选项

    ![采用操作选项卡](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>请参阅

本文档介绍了如何查看安全警报。 有关相关材料，请参阅以下页面：

- [配置警报抑制规则](alerts-suppression-rules.md)
- [通过工作流自动化，自动响应警报和建议](workflow-automation.md)

---
title: 管理 Azure 安全中心的安全事件 |Microsoft Docs
description: 本文档可帮助你使用 Azure 安全中心管理安全事件。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 28a6ea4ed40df909b4d74ff52703babb8e8cd949
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791720"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>在 Azure 安全中心管理安全事件

会审和调查安全警报对于最有经验的安全分析师而言非常耗时。 许多情况下，很难知道从何处着手。 

安全中心使用[分析](security-center-detection-capabilities.md)将信息连接到不同的[安全警报](security-center-managing-and-responding-alerts.md)。 通过使用这些连接，安全中心可以提供攻击活动的单一视图，并提供与其相关的警报，以帮助你了解攻击者的操作和受影响的资源。

本页提供安全中心的事件概述。

## <a name="what-is-a-security-incident"></a>什么是安全事件？

在安全中心，安全事件是对资源的所有警报汇总，与 [网络攻击链](alerts-reference.md#intentions) 模式保持一致。 事件显示在 "[安全警报](security-center-managing-and-responding-alerts.md)" 页中。 选择事件以查看相关警报并获取详细信息。

## <a name="managing-security-incidents"></a>管理安全事件

1. 在 "安全中心概述" 页上，选择 "**安全警报**" 磁贴。 此时会列出事件和警报。 请注意，安全事件对安全警报具有不同的图标。

    ![查看安全事件](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 若要查看详细信息，请选择一个事件。 "**安全事件**" 页显示更多详细信息。 

    [![在 Azure 安全中心响应安全事件](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    安全事件页的左窗格显示有关安全事件的高级信息：标题、严重性、状态、活动时间、说明和受影响的资源。 在受影响的资源旁边，可以看到相关的 Azure 标记。 调查警报时，可以使用这些标记来推断资源的组织上下文。

    右窗格包含 "**警报**" 选项卡，其中包含与此事件相关的安全警报。 

    >[!TIP]
    > 有关特定警报的详细信息，请选择该警报。 

    [![事件的 "采取措施" 选项卡](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    若要切换到 "**执行操作**" 选项卡，请选择选项卡或右窗格底部的按钮。 使用此选项卡执行更多操作，例如：
    - *缓解威胁*-为此安全事件提供手动修正步骤
    - *预防未来攻击*-提供安全建议，以帮助减少攻击面，提高安全状况，防止未来攻击
    - *触发自动响应*-提供触发逻辑应用作为对此安全事件的响应的选项
    - *禁止显示类似警报*-如果警报与组织无关，则提供禁止显示具有类似特征的未来警报的选项 

   > [!NOTE]
   > 同一个警报可以作为事件的一部分存在，也可以作为独立警报显示。

1. 若要修正事件中的威胁，请遵循每个警报附带的补救步骤。


## <a name="next-steps"></a>后续步骤

本页介绍安全中心的安全事件功能。 相关信息，请参阅以下页面：

* [安全中心的威胁防护](threat-protection.md)
* [安全中心的安全警报](security-center-alerts-overview.md)
* [管理和响应安全警报](security-center-managing-and-responding-alerts.md)
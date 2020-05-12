---
title: 使用警报抑制规则来抑制 Azure 安全中心误报或其他不需要的安全警报。
description: 本文介绍如何使用 Azure 安全中心的抑制规则隐藏不需要的安全警报。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 5a5d6a96bd73a67c2611e7f334eba3daeda031ba
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007695"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>抑制 Azure 安全中心的威胁防护警报

此页说明如何使用警报抑制规则在 Azure 安全中心禁止误报或其他不需要的安全警报。

## <a name="availability"></a>可用性

- 版本状态：**预览版**
- 必需的角色：安全管理员和所有者可以创建/删除规则。 安全读者和读取者可以查看规则。
- 云：所有（全球、国家、政府和主权）


## <a name="introduction-to-suppression-rules"></a>抑制规则简介

Azure 安全中心的威胁防护组件检测到环境中任何区域的威胁，并生成安全警报。

当单个警报不有趣或相关时，可以手动将其关闭。 另外，还可以使用 "抑制规则" 功能在将来自动取消类似警报。 通常，您将使用抑制规则来执行以下操作：

- 禁止显示已标识为误报的警报

- 禁止触发过于频繁触发的警报

抑制规则定义应该自动消除警报的条件。

> [!CAUTION]
> 抑制安全警报会降低安全中心的威胁保护。 你应仔细检查任何抑制规则的潜在影响，并在一段时间内监视它。

[![带有警报抑制选项的 Azure 安全中心安全警报页](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>创建抑制规则

可以通过以下几种方式来创建规则，以禁止显示不需要的安全警报：

- 若要取消管理组级别的警报，请使用 Azure 策略

- 若要在订阅级别取消警报，可以使用 Azure 门户或 REST API，如下所述

抑制规则只能消除已在所选订阅上触发的警报。

直接在 Azure 门户中创建规则：

1. 从安全中心的 "安全警报" 页：

    - 找到你不再想要查看的特定警报，并从该警报的省略号菜单（...）中选择 "**创建抑制规则**"：

        [![* * Create 抑制 rule * * 选项](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - 或者，选择页面顶部的 "**禁止显示规则**" 链接，然后在 "禁止显示规则" 页上选择 "新建**禁止显示规则**"：

        !["新建禁止显示规则" 按钮](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. 在 "新建禁止显示规则" 窗格中，输入新规则的详细信息。

    - 你的规则可能会消除**所有资源**上的警报，因此你在将来不会收到此类警报。 
    
    - 如果规则与特定的 IP 地址、进程名称、用户帐户、Azure 资源或位置相关，则规则可以**根据特定条件**解除警报。

    > [!TIP]
    > 如果从特定警报打开 "新建规则" 页，则将在新规则中自动配置警报和订阅。 如果使用了 "新建**禁止显示规则**" 链接，则所选订阅将与门户中的当前筛选器匹配。

    [![禁止显示规则创建窗格](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. 输入规则的详细信息：

    - **名称**-规则的名称。 规则名称必须以字母或数字开头，长度应介于2到50个字符之间，并且不能包含除短划线（-）或下划线（_）以外的其他符号。 
    - 已启用状态或已禁用**状态**。
    - **原因**-如果不满足你的需求，请选择其中一个内置原因或 "其他"。
    - **过期日期**-规则的结束日期和时间。 规则最多可运行六个月。

1. （可选）使用 "**模拟**" 按钮测试规则，以查看在此规则处于活动状态时将消除的警报数。

1. 保存规则。 

## <a name="editing-suppression-rules"></a>编辑禁止显示规则

若要编辑已创建的规则，请使用 "抑制规则" 页。

1. 在安全中心的 "安全警报" 页上，选择页面顶部的 "**禁止显示规则**" 链接。

1. 此时将打开 "禁止显示规则" 页，根据当前选择的订阅列出所有可用的规则。 

    [![禁止显示规则列表](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 若要编辑单个规则，请打开规则的省略号菜单（...），然后选择 "**编辑**"。

1. 进行必要的更改，然后选择 "**应用**"。 

## <a name="deleting-suppression-rules"></a>删除禁止规则

若要删除一个或多个已创建的规则，请使用 "抑制规则" 页。

1. 在安全中心的 "安全警报" 页上，选择页面顶部的 "**禁止显示规则**" 链接。

1. 此时将打开 "禁止显示规则" 页，根据当前选择的订阅列出所有可用的规则。 

1. 若要删除一个规则，请打开该规则的省略号菜单（...），然后选择 "**删除**"。

1. 若要删除多个规则，请选中要删除的规则对应的复选框，然后选择 "**删除**"。

    ![删除一个或多个抑制规则](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>查看已取消的警报

仍将生成与启用的抑制规则匹配的警报，但其状态将设置为 "已取消 **"。** 你可以查看 Azure 门户中的状态，也可以访问安全中心安全警报。 

> [!TIP]
> 对于抑制的警报， [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)不会创建事件。 对于其他 Siem，可以使用警报的状态（"已关闭"）筛选抑制的警报。

使用安全中心的筛选器查看规则已消除的警报。

* 在安全中心的 "安全警报" 页上，打开筛选器选项，**然后选择 "** 已取消"。  

   [![查看已消除的警报](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>使用 API 创建和管理禁止显示规则

可以通过安全中心的 REST API 创建、查看或删除警报抑制规则。 

REST API 中的抑制规则的相关 HTTP 方法如下：

- **PUT**：在指定的订阅中创建或更新禁止显示规则。

- **GET**：

    - 列出为指定订阅配置的所有规则。 此方法返回适用规则的数组。

    - 获取指定订阅上特定规则的详细信息。 此方法返回一个抑制规则。

    - 模拟抑制规则对设计阶段的影响。 此调用标识当规则处于活动状态时，哪些现有警报会被消除。

- **删除**：删除现有规则（但不更改已被其解除的警报的状态）。

有关完整的详细信息和使用示例，请参阅[API 文档](https://docs.microsoft.com/api/securitycenter/)。 


## <a name="next-steps"></a>后续步骤

本文介绍了自动消除不需要的警报的 Azure 安全中心中的禁止显示规则。

有关 Azure 安全中心中的安全警报的详细信息，请参阅以下页面：

- [安全警报和意向终止链](alerts-reference.md)-可以在 Azure 安全中心的威胁防护模块中看到的安全警报的参考指南。
- [Azure 安全中心的威胁防护](threat-protection.md)-Azure 安全中心的威胁防护模块所监视的环境的许多方面的说明。
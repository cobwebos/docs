---
title: Azure 安全中心的工作流自动化 |微软文档
description: 了解如何在 Azure 安全中心创建和自动化工作流
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 972b5415b85e82a5afdaf7d85d3a3bcb9e144d4d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384936"
---
# <a name="workflow-automation"></a>工作流自动化

每个安全程序都包含多个用于事件响应的工作流。 这些流程可能包括通知相关利益干系人、启动变更管理流程以及应用特定的补救步骤。 安全专家建议您尽可能自动执行这些过程的许多步骤。 自动化减少了开销。 它还可以通过确保流程步骤快速、一致、并根据您的预定义要求完成来提高安全性。

本文介绍了 Azure 安全中心的工作流自动化功能。 此功能可以触发安全警报和建议的逻辑应用。 例如，您可能希望安全中心在发生警报时向特定用户发送电子邮件。 您还将了解如何使用[Azure 逻辑应用创建逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。

> [!NOTE]
> 如果您以前在侧边栏上使用 Playbook（预览）视图，您将在新的工作流自动化页面中找到相同的功能以及展开的功能。


## <a name="requirements"></a>要求

* 要使用 Azure 逻辑应用工作流，必须具有以下逻辑应用角色/权限：

    * [逻辑应用操作员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)权限是必需的或逻辑应用读取/触发访问（此角色无法创建或编辑逻辑应用;仅*运行*现有应用）

    * [逻辑应用参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)权限是逻辑应用创建和修改所必需的

* 如果要使用逻辑应用连接器，可能需要其他凭据才能登录到其各自的服务（例如，Outlook/Teams/Slack 实例）


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>创建逻辑应用并定义何时自动运行 

1. 从安全中心的侧边栏中，选择**工作流自动化**。

    [![工作流自动化列表](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    在此页面中，您可以创建新的自动化规则，以及启用、禁用或删除现有规则。  
1. 要定义新工作流，请单击"**添加工作流自动化**"。 

    将显示一个窗格，其中带有新自动化的选项。 您可以在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，您可能希望逻辑应用在生成包含"SQL"的安全警报时运行。
    1. 满足触发器条件时将运行的逻辑应用。 

        [![工作流自动化列表](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. 在"操作"部分中，单击"**创建新操作**"以开始逻辑应用创建过程。

    您将被带往 Azure 逻辑应用。

    [![创建新的逻辑应用](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 输入名称、资源组和位置，然后单击"**创建**"。

1. 在新的逻辑应用中，您可以从安全类别中的内置预定义模板中进行选择。 或者，您可以定义触发此过程时要发生的事件的自定义流。

    在逻辑应用设计器中，支持安全中心连接器中的以下触发器：

    * **创建或触发 Azure 安全中心建议时**
    * **创建或触发 Azure 安全中心警报时** 
    
    > [!TIP]
    > 您可以自定义触发器，使其仅与具有您感兴趣的严重性级别的警报相关联。
    
    > [!NOTE]
    > 如果使用旧触发器"触发对 Azure 安全中心警报的响应"，则工作流自动化功能不会启动逻辑应用。 而是使用上述任一触发器。 

    [![示例逻辑应用](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定义逻辑应用后，返回到工作流自动化定义窗格（"添加工作流自动化"）。 单击 **"刷新**"以确保新的逻辑应用可供选择。

    ![刷新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 选择逻辑应用并保存自动化。 请注意，逻辑应用下拉列表仅显示具有上述支持安全中心连接器的逻辑应用。


## <a name="manually-trigger-a-logic-app"></a>手动触发逻辑应用

您还可以在查看安全警报或任何提供[快速修复修正](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)的建议时手动运行逻辑应用。

要手动运行逻辑应用，请打开警报或支持快速修复修正的建议，然后单击 **"触发逻辑应用**" ：

[![手动触发逻辑应用](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>数据类型架构

要查看传递给逻辑应用实例的安全警报或建议事件的原始事件架构，请访问[工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)。 如果您不使用安全中心的内置逻辑应用连接器上述，而是使用逻辑应用的通用 HTTP 连接器，则此功能非常有用 - 您可以使用事件 JSON 架构在您认为合适的情况下手动分析它。

## <a name="next-steps"></a>后续步骤

在本文中，您了解了创建逻辑应用、在安全中心中手动运行它们以及自动化其执行。 

有关其他相关材料，请参阅以下文章： 

- [Azure 安全中心的安全建议](security-center-recommendations.md)
- [Azure 安全中心的安全警报](security-center-alerts-overview.md)
- [关于 Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [逻辑应用连接器](https://docs.microsoft.com/connectors/)
- [工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)

---
title: Azure 安全中心的工作流自动化 |Microsoft Docs
description: 了解如何在 Azure 安全中心创建和自动化工作流
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 19fcefdfd163bcbd9cd2adfcba6bf6711b9bee18
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041659"
---
# <a name="workflow-automation"></a>工作流自动化

每个安全计划都包含事件响应的多个工作流。 这些流程可能包含通知相关利益干系人、启动更改管理进程，以及应用特定的修正步骤。 安全专家建议你尽可能多地将这些流程自动化。 自动化可降低开销。 它还可以确保按照预定义的要求快速、一致地执行过程步骤，从而提高安全性。

本文介绍 Azure 安全中心的工作流自动化功能。 此功能可根据安全警报和建议触发逻辑应用。 例如，你可能希望安全中心在出现警报时向特定用户发送电子邮件。 你还将了解如何使用[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)创建逻辑应用。

> [!NOTE]
> 如果以前在边栏上使用了 "行动手册" (预览) 视图，则会在 "新建工作流自动化" 页中找到相同的功能和扩展功能。



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式版|
|计价|免费层|
|必需的角色和权限：|订阅上包含导出配置的**读取器**<br>资源组上的**安全管理员角色**或**所有者**<br>还必须对目标资源具有写入权限<br><br>若要使用 Azure 逻辑应用工作流，还必须具有以下逻辑应用角色/权限：<br> - [逻辑应用操作员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)权限是必需的或逻辑应用读取/触发器访问 (此角色无法创建或编辑逻辑应用;仅*运行*现有) <br> - 逻辑[应用参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)权限是创建和修改逻辑应用所必需的<br>如果要使用逻辑应用连接器，可能需要使用其他凭据登录到各自的服务 (例如，Outlook/团队/时差实例) |
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中国 Gov，其他 Gov|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>创建一个逻辑应用，并定义它应自动运行的时间 

1. 从安全中心的边栏中选择 "**工作流自动化**"。

    [![工作流自动化列表](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    在此页上，你可以创建新的自动化规则，还可以启用、禁用或删除现有的规则。  
1. 若要定义新工作流，请单击 "**添加工作流自动化**"。 

    此时会出现一个窗格，其中包含用于新自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能想要在生成包含 "SQL" 的安全警报时运行逻辑应用。
    1. 满足触发条件时将运行的逻辑应用。 

        [![工作流自动化列表](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. 在 "操作" 部分中，单击 "**新建**" 以开始创建逻辑应用过程。

    你将转到 Azure 逻辑应用。

    [![创建新的逻辑应用](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 输入名称、资源组和位置，然后单击 "**创建**"。

1. 在新的逻辑应用中，可以从 "安全" 类别中选择内置的预定义模板。 或者，你可以定义在触发此进程时要发生的自定义流。

    在逻辑应用设计器中，支持安全中心连接器中的以下触发器：

    * **创建或触发 Azure 安全中心建议时**
    * **创建或触发 Azure 安全中心警报时** 
    
    > [!TIP]
    > 你可以自定义触发器，使其仅与你感兴趣的严重性级别相关的警报相关联。
    
    > [!NOTE]
    > 如果在触发对 Azure 安全中心警报的响应时使用旧触发器，则不会通过工作流自动化功能启动逻辑应用。 请改用以上所述的任一触发器。 

    [![示例逻辑应用](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定义逻辑应用后，返回到 "工作流自动化定义" 窗格， ( "添加工作流自动化" ) 。 单击 "**刷新**" 以确保新的逻辑应用可供选择。

    ![刷新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 选择逻辑应用并保存自动化。 请注意，"逻辑应用" 下拉列表仅显示具有上述支持的安全中心连接器的逻辑应用。


## <a name="manually-trigger-a-logic-app"></a>手动触发逻辑应用

你还可以在查看安全警报时手动运行逻辑应用，或者提供提供[快速修复](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)修补程序的任何建议。

若要手动运行逻辑应用，请打开支持快速修复修补的警报或建议，并单击 "**触发逻辑应用**"：

[![手动触发逻辑应用](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>数据类型架构

若要查看传递到逻辑应用实例的安全警报或建议事件的原始事件架构，请访问[工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)。 如果你不使用上面所述的安全中心的内置逻辑应用连接器，而是使用逻辑应用的通用 HTTP 连接器，则可以使用事件 JSON 架构手动分析它，就像你所看到的那样。

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建逻辑应用，如何在安全中心自动执行这些应用并手动运行它们。 

有关其他相关材料，请参阅： 

- [有关如何使用工作流自动化来自动执行安全响应的 Microsoft Learn 模块](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure 安全中心的安全建议](security-center-recommendations.md)
- [Azure 安全中心中的安全警报](security-center-alerts-overview.md)
- [关于 Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [逻辑应用连接器](https://docs.microsoft.com/connectors/)
- [Workflow 自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)

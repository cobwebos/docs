---
title: Azure 安全中心的工作流自动化（预览版） |Microsoft Docs
description: 了解如何在 Azure 安全中心创建和自动化工作流
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 74a33edb358f9b9bf9322c652c0696ef87182725
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521574"
---
# <a name="workflow-automation-preview"></a>工作流自动化（预览）

每个安全计划都包含多个事件响应工作流。 这些过程可能包括通知相关利益干系人、启动更改管理流程，以及应用特定的补救步骤。 安全专家建议您尽可能多地自动执行这些过程的步骤。 自动化可降低开销。 它还可以确保按照预定义的要求快速、一致地执行过程步骤，从而提高安全性。

本文介绍 Azure 安全中心的工作流自动化功能（预览版）。 此预览功能可根据安全警报和建议触发逻辑应用。 例如，你可能希望安全中心在出现警报时向特定用户发送电子邮件。 你还将了解如何使用[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)创建逻辑应用。

> [!NOTE]
> 如果以前在边栏上使用了 "行动手册（预览）" 视图，则可以在 "新工作流自动化（预览版）" 页中找到相同的功能和扩展功能。


## <a name="requirements"></a>要求

* 若要使用 Azure 逻辑应用工作流，你必须具有以下逻辑应用角色/权限：

    * [逻辑应用操作员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)权限是必需的或逻辑应用读取/触发器访问（此角色无法创建或编辑逻辑应用; 仅*运行*现有的应用）

    * 逻辑[应用参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)权限是创建和修改逻辑应用所必需的

* 如果要使用逻辑应用连接器，可能需要使用其他凭据登录到各自的服务（例如，Outlook/团队/时差实例）


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>创建一个逻辑应用，并定义它应自动运行的时间 

1. 在安全中心的边栏中，选择 "**工作流自动化（预览版）** "。

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

    * **创建或触发 Azure 安全中心建议时（预览版）**
    * **创建或触发 Azure 安全中心警报时（预览版）**
    
    > [!NOTE]
    > 如果在触发对 Azure 安全中心警报的响应时使用旧触发器，则不会通过工作流自动化功能启动逻辑应用。 请改用以上所述的任一触发器。 

    [![示例逻辑应用](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定义逻辑应用后，返回到 "工作流自动化定义" 窗格（"添加工作流自动化"）。 单击 "**刷新**" 以确保新的逻辑应用可供选择。

    ![刷新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 选择逻辑应用并保存自动化。


## <a name="manually-trigger-a-logic-app"></a>手动触发逻辑应用

查看安全建议时，还可以手动运行逻辑应用。

若要手动运行逻辑应用，请打开建议，然后单击 "触发逻辑应用（预览版）"：

[![手动触发逻辑应用](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>后续步骤

本文介绍了如何创建逻辑应用，如何在安全中心手动运行这些应用并使其自动执行。 

有关其他相关材料，请参阅以下文章： 

- [Azure 安全中心的安全建议](security-center-recommendations.md)
- [Azure 安全中心中的安全警报](security-center-alerts-overview.md)
- [关于 Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [逻辑应用连接器](https://docs.microsoft.com/connectors/)
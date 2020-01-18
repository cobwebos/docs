---
title: 在 Azure 开发测试实验室中管理自动关闭策略 |Microsoft Docs
description: 了解如何为实验室设置自动关闭策略，以便在虚拟机未使用时自动关闭。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: spelluru
ms.openlocfilehash: a2d0b9bdfba1b96ad42e45d54faf106b2361e29d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264775"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>为 Azure 开发测试实验室中的实验室和计算虚拟机配置自动关闭

本文介绍如何为开发测试实验室中的实验室 Vm 和计算 Vm 配置自动关闭设置。 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>为实验室 Vm 配置自动关闭（开发测试实验室）
借助 Azure 开发测试实验室，可通过管理每个实验室的策略（设置）来控制成本并尽量减少实验中的浪费。 本文介绍如何为实验室帐户配置自动关闭策略，并为实验室帐户中的实验室配置自动关闭设置。 若要了解如何设置每个实验室策略，请参阅[在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。  

### <a name="set-auto-shut-down-policy-for-a-lab"></a>设置实验室的自动关闭策略
实验室所有者可针对实验室中的所有 VM 配置关机计划。 这样可以节省成本，因为未使用（空闲）的计算机不会运行。 可以集中针对所有实验室 VM 实施关机策略，省得实验室用户付出精力针对每个计算机设置计划。 使用此功能可以针对实验室计划设置策略，一开始不为实验室用户提供任何控制权，逐渐为他们提供完全控制权。 实验室所有者可通过以下步骤配置此策略：

1. 在实验室的主页上，选择“配置和策略”。
2. 在左侧菜单的“计划”部分选择“自动关机策略”。
3. 选择一个选项。 以下部分更详细地介绍了这些选项：设置的策略只会应用到实验室中新建的 VM，而不会应用到现有的 VM。 

    ![自动关闭策略选项](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>配置自动关闭设置
通过允许你指定此实验室的 Vm 关闭的时间，自动关闭策略有助于最大程度地减少实验室浪费。

若要查看（和更改）实验室策略，请按以下步骤操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，并从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需的实验室。   
4. 选择“配置和策略”。

    ![策略设置窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在实验室的 "**配置和策略**" 窗格中，选择 "**计划**" 下**的 "自动关闭**"。
   
    ![自动关闭](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 选择“开启”启用此策略，选择“关闭”禁用此策略。
7. 如果启用此策略，请指定要关闭当前实验室中所有 VM 的时间（和时区）。
8. 指定 **"是"** 或 "**否**"，以在指定的自动关闭时间之前30分钟发送通知。 如果选择“是”，请输入 Webhook URL 终结点或电子邮件地址，指定要将通知发布或发送到的位置。 用户会收到通知，其中提供了延迟关闭的选项。 有关详细信息，请参阅 "[通知](#notifications)" 部分。 
9. 选择“保存”。

    默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 要从特定 VM 中删除此设置，请打开 VM 的管理窗格，然后更改其**自动关闭**设置。
    
> [!NOTE]
> 如果在当前计划时间的30分钟内更新实验室或特定实验室虚拟机的自动关闭计划，则更新的关闭时间将适用于下一天的计划。 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>用户设置计划，并可选择禁用
如果在此策略中设置了你的实验室，则实验室用户可以替代或选择禁用实验室计划。 此选项授予实验室用户对其 VM 的自动关机计划的完全控制权。 实验室用户在其 VM 自动关机计划页中看不到任何变化。

![自动关闭策略选项-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>用户设置计划，但无法选择禁用
如果在此策略中设置了你的实验室，则实验室用户可以替代实验室计划。 但是，他们无法选择禁用自动关机策略。 此选项可确保实验室中的每台计算机根据自动关机计划运行。 实验室用户可以更新其 VM 的自动关机计划，并设置关机通知。

![自动关闭策略选项-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>用户无法控制实验室管理员设置的计划
如果在此策略中设置了你的实验室，则实验室用户无法替代或选择禁用实验室计划。 此选项可让实验室管理员全权控制实验室中每台计算机的计划。 实验室用户只能针对其 VM 设置自动关机通知。

![自动关闭策略选项-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>通知
在实验室所有者设置自动关闭后，将在自动关闭触发之前30分钟将通知发送给实验室用户，前提是其任何 Vm 会受到影响。 使用此选项，实验室用户可以在关机前保存其工作。 此通知还会为每个 VM 提供以下操作的链接：

- 暂时跳过自动关闭
- 暂停自动关闭一小时或2小时，以便他们能够继续使用 VM。

通知将通过配置的 web 挂钩终结点或由实验室所有者在自动关闭设置中指定的电子邮件地址发送。 Webhook 允许生成或设置订阅特定事件的集成。 触发其中一个事件时，开发测试实验室会将 HTTP POST 有效负载发送到 webhook 的已配置 URL。 有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

建议你使用 web 挂钩，因为它们广泛受各种应用（例如，时差、Azure 逻辑应用等）的支持。并允许你实现自己的发送通知的方式。 例如，本文介绍如何使用 Azure 逻辑应用从电子邮件中获取自动关闭通知。 首先，让我们快速完成在实验室中启用自动关闭通知的基本步骤。   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>创建一个逻辑应用，用于接收电子邮件通知
[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)提供了许多现成的连接器，使你可以轻松地将服务与其他客户端（如 Office 365 和 twitter）相集成。 在高级别上，为电子邮件通知设置逻辑应用的步骤可以分为四个阶段： 

- 创建逻辑应用。 
- 配置内置模板。
- 与你的电子邮件客户端集成
- 获取 Webhook URL。

### <a name="create-a-logic-app"></a>创建逻辑应用
若要开始，请使用以下步骤在 Azure 订阅中创建逻辑应用：

1. 在左侧菜单中选择 " **+ 创建资源**"，选择 "**集成**"，然后选择 "**逻辑应用**"。 

    ![新建逻辑应用菜单](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 在 "**逻辑应用-创建**" 页上，执行以下步骤： 
    1. 输入逻辑应用的**名称**。
    2. 选择 **Azure 订阅**。
    3. 创建新的资源组，或选择现有资源组。 
    4. 选择逻辑应用的**位置**。 

        ![新建逻辑应用-设置](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 在**通知**中，选择 "前往通知上的**资源**"。 

    ![转到资源](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 在 "**部署工具**" 类别下选择**逻辑应用设计器**。

    ![选择 HTTP 请求/响应](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. 在 " **HTTP 请求-响应**" 页上，选择 "**使用此模板**"。 

    ![选择 "使用此模板" 选项](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 将以下 JSON 复制到**请求正文 JSON 架构**部分： 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![请求正文 JSON 架构](./media/devtest-lab-auto-shutdown/request-json.png)
7. 在设计器中选择 " **+ 新步骤**"，然后执行以下步骤：
    1. 搜索**Office 365 Outlook-发送电子邮件**。 
    2. 选择 "从**操作** **发送电子邮件**"。 
    
        ![发送电子邮件选项](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 选择 **"登录"** 以登录到你的电子邮件帐户。 
    4. 选择 **"字段"，然后选择 "** 所有者"。
    5. 选择 "**使用者**"，并输入电子邮件通知的主题。 例如： "Shutdown machine vmName for Lab： labName"。
    6. 选择 "**正文**"，并定义电子邮件通知的正文内容。 例如： "vmName 计划在15分钟后关闭。 单击 "URL" 跳过此关闭操作。 延迟关机一小时： delayUrl60。 延迟关机2小时： delayUrl120。 "

        ![请求正文 JSON 架构](./media/devtest-lab-auto-shutdown/email-options.png)
1. 在工具栏上选择“保存”。 现在，你可以复制**HTTP POST URL**。 选择 "复制" 按钮，将 URL 复制到剪贴板。 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>为计算 Vm 配置自动关闭

1. 在 "**虚拟机**" 页上，选择左侧菜单上的 "**自动关闭**"。 
2. 在 "**自动关闭**" 页上，选择 **"打开**" 以启用此策略，并选择 "**关闭**" 以禁用它。
3. 如果启用此策略，请指定应关闭 VM 的**时间** **（和时区**）。
4. 指定 **"是"** 或 "**否**"，以在指定的自动关闭时间之前30分钟发送通知。 如果选择“是”，请输入 Webhook URL 终结点或电子邮件地址，指定要将通知发布或发送到的位置。 用户会收到通知，其中提供了延迟关闭的选项。 有关详细信息，请参阅 "[通知](#notifications)" 部分。 
9. 选择“保存”。

    ![为计算 VM 配置自动关闭](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

## <a name="next-steps"></a>后续步骤
若要了解如何设置所有策略，请参阅[在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。


---
title: 在 Azure 开发人员测试实验室中管理自动关闭策略 |微软文档
description: 了解如何为实验室设置自动关闭策略，以便在虚拟机不使用时自动关闭。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264775"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中配置实验室和计算虚拟机的自动关机

本文介绍如何在 DevTest 实验室中为实验室 VM 配置自动关机设置并计算 VM。 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>为实验室 VM 配置自动关机（开发人员测试实验室）
借助 Azure 开发测试实验室，可通过管理每个实验室的策略（设置）来控制成本并尽量减少实验中的浪费。 本文介绍如何为实验室帐户配置自动关闭策略，并为实验室帐户中的实验室配置自动关闭设置。 若要了解如何设置每个实验室策略，请参阅[在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。  

### <a name="set-auto-shut-down-policy-for-a-lab"></a>为实验室设置自动关闭策略
实验室所有者可针对实验室中的所有 VM 配置关机计划。 这样可以节省成本，因为未使用（空闲）的计算机不会运行。 可以集中针对所有实验室 VM 实施关机策略，省得实验室用户付出精力针对每个计算机设置计划。 使用此功能可以针对实验室计划设置策略，一开始不为实验室用户提供任何控制权，逐渐为他们提供完全控制权。 实验室所有者可通过以下步骤配置此策略：

1. 在实验室的主页上，选择“配置和策略”。****
2. 在左侧菜单的“计划”部分选择“自动关机策略”。********
3. 选择一个选项。 以下部分更详细地介绍了这些选项：设置的策略只会应用到实验室中新建的 VM，而不会应用到现有的 VM。 

    ![自动关闭策略选项](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>配置自动关机设置
自动关闭策略允许您指定此实验室的 VM 关闭时间，从而有助于最大限度地减少实验室浪费。

若要查看（和更改）实验室策略，请按以下步骤操作：

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 选择**所有服务**，然后从列表中选择**DevTest 实验室**。
3. 从实验室列表，选择所需的实验室。   
4. 选择“配置和策略”。****

    ![策略设置窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在实验室的 **"配置和策略"** 窗格中，选择 **"计划**"下的**自动关闭**。
   
    ![自动关机](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 选择“开启”**** 启用此策略，选择“关闭”**** 禁用此策略。
7. 如果启用此策略，请指定要关闭当前实验室中所有 VM 的时间（和时区）。
8. 指定 **"是**"或 **"否**"选项，用于在指定自动关闭时间前 30 分钟发送通知的选项。 如果选择“是”，请输入 Webhook URL 终结点或电子邮件地址，指定要将通知发布或发送到的位置****。 用户会收到通知，其中提供了延迟关闭的选项。 有关详细信息，请参阅[通知](#notifications)部分。 
9. 选择“保存”。****

    默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 要从特定 VM 中删除此设置，请打开 VM 的管理窗格并更改其**自动关机**设置。
    
> [!NOTE]
> 如果在当前计划时间的 30 分钟内更新实验室或特定实验室虚拟机的自动关机计划，则更新后的关机时间将适用于第二天的计划。 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>用户设置计划，并可选择禁用
如果在此策略中设置了你的实验室，则实验室用户可以替代或选择禁用实验室计划。 此选项授予实验室用户对其 VM 的自动关机计划的完全控制权。 实验室用户在其 VM 自动关机计划页中看不到任何变化。

![自动关闭策略选项 - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>用户设置计划，但无法选择禁用
如果在此策略中设置了你的实验室，则实验室用户可以替代实验室计划。 但是，他们无法选择禁用自动关机策略。 此选项可确保实验室中的每台计算机根据自动关机计划运行。 实验室用户可以更新其 VM 的自动关机计划，并设置关机通知。

![自动关闭策略选项 - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>用户无法控制实验室管理员设置的计划
如果在此策略中设置了你的实验室，则实验室用户无法替代或选择禁用实验室计划。 此选项可让实验室管理员全权控制实验室中每台计算机的计划。 实验室用户只能针对其 VM 设置自动关机通知。

![自动关闭策略选项 - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>通知
实验室所有者设置自动关闭后，如果任何 VM 将受到影响，将在自动关闭触发前 30 分钟向实验室用户发送通知。 此选项使实验室用户有机会在关闭之前保存他们的工作。 通知还为每个 VM 提供了以下操作的链接：

- 此时跳过自动关闭
- 将自动关闭暂停一小时或 2 小时，以便他们可以在 VM 上继续工作。

通知通过配置的 Web 挂钩终结点或实验室所有者在自动关闭设置中指定的电子邮件地址发送。 Webhook 允许您构建或设置订阅某些事件的集成。 触发其中一个事件时，DevTest Labs 将向 Webhook 的配置 URL 发送 HTTP POST 有效负载。 有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

我们建议您使用 Web 挂钩，因为它们受到各种应用（例如，Slack、Azure 逻辑应用等）的广泛支持，并允许您实现自己的发送通知方式。 例如，本文将介绍如何使用 Azure 逻辑应用从电子邮件中获取自动关闭通知。 首先，让我们快速完成在实验室中启用自动关闭通知的基本步骤。   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>创建接收电子邮件通知的逻辑应用
[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)提供了许多开箱即用的连接器，使服务与其他客户端（如 Office 365 和 twitter）集成变得容易。 在高级别上，为电子邮件通知设置逻辑应用的步骤可以分为四个阶段： 

- 创建逻辑应用。 
- 配置内置模板。
- 与您的电子邮件客户端集成
- 获取 Webhook URL。

### <a name="create-a-logic-app"></a>创建逻辑应用
要开始，请使用以下步骤在 Azure 订阅中创建逻辑应用：

1. 选择 = 在左侧菜单上**创建资源**，选择 **"集成**"并选择 **"逻辑应用**"。 

    ![新逻辑应用菜单](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 在**逻辑应用 - 创建**页面上，按照以下步骤操作： 
    1. 输入逻辑应用**的名称**。
    2. 选择 Azure**订阅**。
    3. 创建新的资源组****，或选择现有资源组。 
    4. 选择逻辑应用**的位置**。 

        ![新逻辑应用 - 设置](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 在**通知**中，选择 **"转到**通知上的资源"。 

    ![转到资源](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 在 **"部署工具"** 类别下选择**逻辑应用设计器**。

    ![选择 HTTP 请求/响应](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. 在**HTTP 请求-响应**页上，选择 **"使用此模板**"。 

    ![选择"使用此模板"选项](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
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
7. 选择 = 设计器中**的新步骤**，然后按照以下步骤操作：
    1. 搜索**Office 365 Outlook - 发送电子邮件**。 
    2. 选择**从****操作**发送电子邮件。 
    
        ![发送电子邮件选项](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 选择 **"登录**"以登录电子邮件帐户。 
    4. 选择 **"要"** 字段，然后选择所有者。
    5. 选择**SUBJECT**，并输入电子邮件通知的主题。 例如："关闭实验室的计算机 vmName 名称：实验室名称"。
    6. 选择**BODY**，并定义电子邮件通知的正文内容。 例如："vmName 计划在 15 分钟内关闭。 单击：URL 跳过此关闭。 延迟关闭一小时：延迟Url60。 延迟关闭 2 小时：延迟 Url120。

        ![请求正文 JSON 架构](./media/devtest-lab-auto-shutdown/email-options.png)
1. 在工具栏上选择“保存”。**** 现在，您可以复制**HTTP POST URL。** 选择复制按钮以将 URL 复制到剪贴板。 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>为计算 VM 配置自动关机

1. 在 **"虚拟机"** 页上，选择左侧菜单上的 **"自动关闭**"。 
2. 在 **"自动关闭"** 页上，选择 **"打开**"以启用此策略，**然后选择"关闭**"以禁用它。
3. 如果启用此策略，请指定应关闭 VM**的时间**（和**时区**）。
4. 指定 **"是**"或 **"否**"选项，用于在指定自动关闭时间前 30 分钟发送通知的选项。 如果选择“是”，请输入 Webhook URL 终结点或电子邮件地址，指定要将通知发布或发送到的位置****。 用户会收到通知，其中提供了延迟关闭的选项。 有关详细信息，请参阅[通知](#notifications)部分。 
9. 选择“保存”。****

    ![为计算 VM 配置自动关机](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

## <a name="next-steps"></a>后续步骤
要了解如何设置所有策略，请参阅在[Azure 开发人员测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。


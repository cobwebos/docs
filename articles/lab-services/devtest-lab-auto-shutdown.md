---
title: 管理 Azure 开发测试实验室中的自动关闭策略 |Microsoft Docs
description: 了解如何设置自动关闭策略的实验室，以便虚拟机自动关闭时未使用。
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873985"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>管理 Azure 开发测试实验室的实验室中的自动关闭策略
借助 Azure 开发测试实验室，可通过管理每个实验室的策略（设置）来控制成本并尽量减少实验中的浪费。 本文介绍如何配置实验室帐户的自动关闭策略和在实验室帐户中配置的某个实验室的自动关闭设置。 若要了解如何设置每个实验室策略，请参阅[在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>设置自动关闭策略的实验室
实验室所有者可针对实验室中的所有 VM 配置关机计划。 这样可以节省成本，因为未使用（空闲）的计算机不会运行。 可以集中针对所有实验室 VM 实施关机策略，省得实验室用户付出精力针对每个计算机设置计划。 使用此功能可以针对实验室计划设置策略，一开始不为实验室用户提供任何控制权，逐渐为他们提供完全控制权。 实验室所有者可通过以下步骤配置此策略：

1. 在实验室的主页上，选择“配置和策略”。 
2. 在左侧菜单的“计划”部分选择“自动关机策略”。  
3. 选择一个选项。 以下各节提供了有关这些选项的更多详细信息：设置策略仅适用于在实验室中创建的新 Vm 和不到现有的 Vm。 

    ![自动关闭策略选项](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>配置自动关闭设置
自动关闭策略有助于通过它可以指定此实验室 Vm 关闭的时间，最小化实验室浪费。

若要查看（和更改）实验室策略，请按以下步骤操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
3. 从实验室列表，选择所需的实验室。   
4. 选择“配置和策略”。 

    ![策略设置窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在实验室的**配置和策略**窗格中，选择**自动关闭**下**计划**。
   
    ![自动关闭](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 选择“开启”  启用此策略，选择“关闭”  禁用此策略。
7. 如果启用此策略，请指定要关闭当前实验室中所有 VM 的时间（和时区）。
8. 指定**是**或**否**选项来指定自动关闭时间前 15 分钟发送一条通知。 如果选择“是”，请输入 Webhook URL 终结点或电子邮件地址，指定要将通知发布或发送到的位置  。 用户会收到通知，其中提供了延迟关闭的选项。 有关详细信息，请参阅[通知](#notifications)部分。 
9. 选择“保存”。 

    默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 若要从特定 VM 中删除此设置，请打开 VM 的管理窗格，并更改其**自动关闭**设置。

### <a name="user-sets-a-schedule-and-can-opt-out"></a>用户设置计划，并可选择禁用
如果在此策略中设置了你的实验室，则实验室用户可以替代或选择禁用实验室计划。 此选项授予实验室用户对其 VM 的自动关机计划的完全控制权。 实验室用户在其 VM 自动关机计划页中看不到任何变化。

![自动关闭策略选项-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>用户设置计划，但无法选择禁用
如果在此策略中设置了你的实验室，则实验室用户可以替代实验室计划。 但是，他们无法选择禁用自动关机策略。 此选项可确保实验室中的每台计算机根据自动关机计划运行。 实验室用户可以更新其 VM 的自动关机计划，并设置关机通知。

![自动关闭策略选项-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>用户无法控制实验室管理员设置的计划
如果在此策略中设置了你的实验室，则实验室用户无法替代或选择禁用实验室计划。 此选项可让实验室管理员全权控制实验室中每台计算机的计划。 实验室用户只能针对其 VM 设置自动关机通知。

![自动关闭策略选项-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>通知
实验室所有者通过设置自动关闭，通知将发送到实验室用户如果任何其虚拟机触发自动关闭之前的 15 分钟后将会受到影响。 此选项使实验室用户有机会保存在关闭之前其工作。 通知还提供了链接，为每个 VM 的下列操作：

- 跳过这一次自动关闭
- 推迟自动关闭一小时或 2 个小时为单位，以便它们可以在 VM 上继续工作。

实验室所有者的自动关闭设置中指定的电子邮件地址或通过配置的 web 挂钩终结点发送通知。 通过 Webhook 可以生成或设置订阅特定事件的集成。 触发这些事件之一时，开发测试实验室将向已配置的 webhook 的 URL 发送 HTTP POST 负载。 有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

我们建议你使用 web 挂钩，因为它们广泛支持的各种应用程序 （例如，Slack、 Azure 逻辑应用和等等。），并允许您实现您自己的方式用于发送通知。 例如，本文逐步讲解如何使用 Azure 逻辑应用收到的电子邮件自动关闭通知。 首先，让我们快速转的基本步骤，以便在实验室中的自动关闭通知。   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>创建一个逻辑应用，接收电子邮件通知
[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)提供了多个开箱连接器，就可以轻松将服务集成与其他客户端，Office 365 和 twitter 等。 在高级别中，若要设置电子邮件通知逻辑应用的步骤可分为四个阶段： 

- 创建逻辑应用。 
- 内置的模板配置。
- 将与你的电子邮件客户端集成
- 获取 Webhook URL。

### <a name="create-a-logic-app"></a>创建逻辑应用
若要开始，请使用以下步骤在 Azure 订阅中创建逻辑应用：

1. 选择 **+ 创建资源**在左侧菜单中，选择**集成**，然后选择**逻辑应用**。 

    ![新的逻辑应用菜单](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 上**逻辑应用-创建**页上，执行以下步骤： 
    1. 输入**名称**为逻辑应用。
    2. 选择 **Azure 订阅**。
    3. 创建新的资源组  ，或选择现有资源组。 
    4. 选择**位置**为逻辑应用。 

        ![新的逻辑应用的设置](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 在中**通知**，选择**转到资源**通知。 

    ![转到资源](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 选择**逻辑应用设计器**下**部署工具**类别。

    ![选择 HTTP 请求/响应](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. 上**HTTP 请求-响应**页上，选择**使用此模板**。 

    ![选择使用此模板选项](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 复制以下 json 反序列化为**请求正文 JSON 架构**部分： 

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
7. 选择 **+ 新步骤**在设计器中，执行以下步骤：
    1. 搜索**Office 365 Outlook-发送电子邮件**。 
    2. 选择**发送一封电子邮件**从**操作**。 
    
        ![发送电子邮件选项](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 选择**登录**登录到电子邮件帐户。 
    4. 选择**TO**字段，并选择所有者。
    5. 选择**使用者**，并输入电子邮件通知的主题。 例如："关闭实验室的机 vmName: labName。"
    6. 选择**正文**，并定义电子邮件通知的正文内容。 例如:"vmName 计划在 15 分钟内关闭。 通过单击跳过此次关闭：URL。 一小时的延迟关闭： delayUrl60。 2 小时的延迟关闭： delayUrl120。"

        ![请求正文 JSON 架构](./media/devtest-lab-auto-shutdown/email-options.png)
1. 在工具栏上选择“保存”。  现在，你可以将复制**HTTP POST URL**。 选择复制按钮以将 URL 复制到剪贴板。 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>后续步骤
若要了解如何设置的所有策略，请参阅[在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。

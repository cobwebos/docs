---
title: Azure 计费和成本管理预算方案 | Microsoft Docs
description: 了解如何通过 Azure 自动化根据特定预算阈值关闭 VM。
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 7/25/2018
ms.author: erikre
ms.openlocfilehash: 19d8fbf82410136fd6a50a2088dd9b774dd6cb27
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259205"
---
# <a name="manage-costs-with-azure-budgets"></a>使用 Azure 预算管理成本

成本控制是最大化云投资价值的关键部分。 以下几种方案中，成本可见性、报告和基于成本的业务流程对于持续的业务操作至关重要。 [Azure 成本管理 API](https://docs.microsoft.com/rest/api/consumption/) 提供了一组 API，用于支持每种方案。 这些 API 提供使用情况详细信息，可让你查看更精确的实例级成本。

成本控制中通常会用到预算。 可在 Azure 中限制预算范围。 例如，可根据订阅、资源组或资源集合缩小预算视图。 除了使用预算 API 在达到预算阈值时通过电子邮件进行通知外，还可使用 [Azure Monitor 操作组](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)来触发由于预算事件而安排的一组操作。

对于运行非关键工作负载的客户，如果他们想要管理预算，并在查看月度发票时预测成本，可能会使用常见的预算方案。 这种方案需要一些基于成本的资源业务流程，这些资源属于 Azure 环境。 在此方案中，每月的订阅预算设置为 1000 美元。 另外，通知阈值设置为触发几个业务流程。 此方案的成本阈值开始为 80%，这会停止“可选”资源组中的所有 VM。 然后，达到 100% 成本阈值时，将停止所有 VM 实例。
若要配置此方案，可按照本教程中各节提供的步骤完成以下操作。 

通过本教程中的这些操作，你可以：

- 创建 Azure 自动化 Runbook，以使用 Webhook 停止 VM。
- 创建根据预算阈值触发的 Azure 逻辑应用并调用包含正确参数的 runbook。
- 创建 Azure Monitor 操作组，该操作组将配置为在达到预算阈值时触发 Azure 逻辑应用。
- 创建具有所需阈值的 Azure 预算，并将其绑定到操作组。

## <a name="create-an-azure-automation-runbook"></a>创建 Azure 自动化 Runbook

[Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)是一种服务，可用于编写大部分资源管理任务并按计划或按需运行这些任务。 作为此方案的一部分，你将创建用于停止 VM 的 [Azure 自动化 runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)。 请使用[库](https://docs.microsoft.com/azure/automation/automation-runbook-gallery)中的[停止 Azure V2 VM](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 图形 runbook 来构建此方案。 通过将此 runbook 导入 Azure 帐户并发布，可以在达到预算阈值时停止 VM。 

### <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

1.  使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com/)。
2.  单击 Azure 左上角的“创建资源”按钮。
3.  选择“管理工具” > “自动化”。
    > [!NOTE]
    > 如果没有 Azure 帐户，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。
4.  输入帐户信息。 对于**创建 Azure 运行方式帐户**，请选择**是**，以便自动启用可简化向 Azure 进行身份验证所需的设置。
5.  完成后，请单击**创建**，启动自动化帐户部署。

### <a name="import-the-stop-azure-v2-vms-runbook"></a>导入“停止 Azure V2 VM”runbook

使用 [Azure 自动化 runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)，从库中导入[停止 Azure V2 VM](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 图形 runbook。

1.  使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com/)。
2.  选择“所有服务” > “自动化帐户”，以打开自动化帐户。 然后，选择“自动化帐户”。
3.  从“流程自动化”部分单击“Runbook 库”。
4.  将“库源”设置为“脚本中心”，然后选择“确定”。
5.  在 Azure 门户中找到并选择[停止 Azure V2 VM](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 库项。
6.  单击“导入”按钮以显示“导入”边栏选项卡，然后选择“确定”。 此时将显示 runbook 概览边栏选项卡。
7.  Runbook 完成导入过程后，选择“编辑”以显示图形 runbook 编辑器和发布选项。
    
    ![Azure - 编辑图形 runbook](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  单击“发布”按钮以发布该 runbook，出现提示时选择“是”。 发布 runbook 时，可以用草稿版本替代任何现有的已发布版本。 在本例中，还没有已发布版本，因为才创建了 runbook。 

    有关发布 runbook 的详细信息，请参阅[创建图形 runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical)。

## <a name="create-webhooks-for-the-runbook"></a>为 runbook 创建 Webhook

使用[停止 Azure V2 VM](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 图形 runbook，你将创建两个 Webhook 以通过单个 HTTP 请求在 Azure 自动化中启动 runbook。 第一个 Webhook 将在达到 80% 预算阈值时调用 runbook，并将资源组名称作为参数，允许停止可选的 VM。 然后，第二个 Webhook 将调用不带任何参数（达到 100% 时）的 runbook，这将停止所有剩余的 VM 实例。

1.  在 [Azure 门户](https://portal.azure.com/)的“Runbook”页中，单击显示 runbook 概览边栏选项卡的 StopAzureV2Vm runbook。 
2.  单击页顶部的“Webhook”，打开“添加 Webhook”边栏选项卡。
3.  单击“新建 Webhook”，打开“创建新 Webhook”边栏选项卡。
4.  将 Webhook 的“名称”设置为“Optional”。 “启用”属性必须为“是”。 无需更改“到期日期”值。 有关 Webhook 属性的详细信息，请参阅 [Webhook 详细信息](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook)。
5.  单击 URL 值旁边的复制图标，复制 Webhook 的 URL。 
    > [!IMPORTANT]
    > 将名为“Optional”的 Webhook 的 URL 保存在安全的位置。 本教程的后面部分会用到此值。 出于安全原因，创建 Webhook 后，就不能再次查看或检索该 URL。
6.  单击“确定”创建新的 Webhook。
7.  单击“配置参数并运行设置”，查看该 runbook 的参数值。 
    > [!NOTE]
    > 如果 Runbook 包含必需的参数，除非提供了相应的值，否则无法创建 Webhook。
8.  单击“确定”接受 Webhook 参数值。
9.  单击“创建”以创建 Webhook。
10. 接下来，请按上述步骤创建名为“Complete”的第二个 Webhook。 
    > [!IMPORTANT]
    > 请务必保存这两个 Webhook URL，供本教程后面部分使用。 出于安全原因，创建 Webhook 后，就不能再次查看或检索该 URL。

现在你应有两个已配置的 Webhook，它们都可以使用保存的 URL。 

![Webhook - Optional 和 Complete](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

现在已完成 Azure 自动化设置。 可以使用简单的 Postman 测试来测试 Webhook，验证 Webhook 是否正常工作。 接下来，必须为业务流程创建逻辑应用。

## <a name="create-an-azure-logic-app-for-orchestration"></a>为业务流程创建 Azure 逻辑应用

逻辑应用有助于生成、计划和自动完成工作流形式的流程，适合跨企业或组织集成应用、数据、系统和服务。 在此方案中，所创建的[逻辑应用](https://docs.microsoft.com/azure/logic-apps/)只会调用创建的自动化 Webhook。 

可将“预算”设置为在达到指定的阈值时触发通知。 可提供触发通知的多个阈值，逻辑应用将为你演示基于达到的阈值执行不同操作的能力。 本例中将设置一个方案，其中你会收到两个通知，达到预算的 80% 时会收到第一个通知，达到预算的 100% 时会收到第二个通知。 逻辑应用将用于关闭资源组中的所有 VM。 首先，将达到“Optional”阈值的 80%，然后达到第二个阈值，此时将关闭订阅中的所有 VM。

可通过逻辑应用为 HTTP 触发器提供示例架构，但需要设置 Content-Type 标头。 由于操作组没有 Webhook 的自定义标头，必须在单独的步骤中解析有效负载。 需使用 Parse 操作并为其提供有效负载示例。

### <a name="create-the-logic-app"></a>创建逻辑应用

逻辑应用会执行几项操作。 下表提供了逻辑应用将执行的一组高级操作：
- 在收到 HTTP 请求时识别
- 分析传入的 JSON 数据，确定已达到的阈值
- 使用条件语句检查阈值金额是否已达到预算范围的 80% 或更多，但不能大于或等于 100%。
    - 如果已达到此阈值金额，则使用名为“Optional”的 Webhook 发送 HTTP POST。 此操作将关闭“可选”组中的 VM。
- 使用条件语句检查阈值金额是否已达到或超过预算值的 100%。
    - 如果已达到此阈值金额，则使用名为“Complete”的 Webhook 发送 HTTP POST。 此操作将关闭剩下的所有 VM。

创建要执行上述步骤的逻辑应用时需要以下步骤：

1.  在 [Azure 门户](https://portal.azure.com/)中，选择“创建资源” > “集成” > “逻辑应用”。
    
    ![Azure - 选择“逻辑应用”资源](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  在“创建逻辑应用”边栏选项卡中，提供创建逻辑应用所需的详细信息，选择“固定到仪表板”，然后单击“创建”。 
    
    ![Azure - 创建逻辑应用](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

在 Azure 部署逻辑应用以后，逻辑应用设计器会打开并显示一个包含简介视频和常用触发器的边栏选项卡。 

### <a name="add-a-trigger"></a>添加触发器

每个逻辑应用都必须从触发器开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。 操作是在触发器之后发生的所有步骤。 

1.  在“逻辑应用设计器”边栏选项卡的“模板”下，选择“空白逻辑应用”。
2.  添加[触发器](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts)，具体方法是在“逻辑应用设计器”搜索框中输入“http request”，查找并选择名为“请求 - 收到 HTTP 请求时”的触发器。
    
    ![Azure - 逻辑应用 - Http 触发器](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png) 
3.  选择“新建步骤” > “添加操作”。 
    
    ![Azure - 新建步骤 - 添加操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png) 
4.  在“逻辑应用设计器”搜索框中搜索“分析 JSON”，查找并选择“数据操作 - 分析 JSON”[操作](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts)。 
    
    ![Azure - 逻辑应用 - 添加“分析 JSON”操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png) 
5.  输入“Payload”作为“分析 JSON”有效负载的内容名称。
6.  在“分析 JSON”框中，选择“使用有效负载示例生成架构”选项。
    
    ![Azure - 逻辑应用 - 使用 JSON 数据示例生成架构](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png) 
7.  将以下 JSON 有效负载示例粘贴到文本框中：`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
    
    显示的文本框如下所示：
        
    ![Azure - 逻辑应用 - JSON 有效负载示例](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png) 
8.  单击“Done”（完成） 。

### <a name="add-the-first-conditional-action"></a>添加第一个条件操作

使用条件语句检查阈值金额是否已达到预算范围的 80% 或更多，但不能大于或等于 100%。 如果已达到此阈值金额，则使用名为“Optional”的 Webhook 发送 HTTP POST。 此操作将关闭“可选”组中的 VM。

1.  选择“新建步骤” > “添加条件”。
    
    ![Azure - 逻辑应用 - 添加条件](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png) 
2.  在“条件”框中，单击包含“选择值”的文本框，以显示可用值列表。
    
    ![Azure - 逻辑应用 -“条件”框](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png) 
        
3.  单击列表顶部的“表达式”并在表达式编辑器中输入以下表达式：`float()`
    
    ![Azure - 逻辑应用 - Float 表达式](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png) 
        
4.  选择“动态内容”，将光标置于括号 () 内，然后从列表中选择“NotificationThresholdAmount”以填充整个表达式。 
    
    表达式如下：<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
    
5.  选择“确定”，以设置表达式。 
6.  在“条件”下拉框中选择“大于或等于”。
7.  在条件的“选择值”框中，输入 `.8`。
    
    ![Azure - 逻辑应用 - 包含值的 Float 表达式](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png) 
        
8.  点击“条件”框中的“添加” > “添加行”，以添加条件的其他部分。
9.  在“条件”框中，单击包含“选择值”的文本框。
10. 单击列表顶部的“表达式”并在表达式编辑器中输入以下表达式：`float()`
11. 选择“动态内容”，将光标置于括号 () 内，然后从列表中选择“NotificationThresholdAmount”以填充整个表达式。 
12. 选择“确定”，以设置表达式。 
13. 在“条件”的下拉框中选择“小于”。
14. 在条件的“选择值”框中，输入 `1`。
    
    ![Azure - 逻辑应用 - 包含值的 Float 表达式](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png) 
        
15. 在“如果为 true”框中，选择“添加操作”。 将添加一个可关闭可选 VM 的 HTTP POST 操作。
    
    ![Azure - 逻辑应用 - 添加操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png) 
        
16. 输入“HTTP”以搜索 HTTP 操作，然后选择“HTTP - HTTP”操作。
    
    ![Azure - 逻辑应用 - 添加 HTTP 操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png) 
        
17. 选择“Post”作为“方法”值。
18. 对于之前在本教程中创建的名为“Optional”的 Webhook，请输入其 URL 作为 URI 值。
    
    ![Azure - 逻辑应用 - HTTP 操作 URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png) 
        
19. 在“如果为 true”框中，选择“添加操作”。 将添加一个电子邮件操作，该操作会发送一封电子邮件，通知收件人已关闭可选 VM。
20. 搜索“发送电子邮件”，然后根据所用的电子邮件服务选择“发送电子邮件”操作。
        
    ![Azure - 逻辑应用 -“发送电子邮件”操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png) 
    
    对于个人 Microsoft 帐户，请选择“Outlook.com”。 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。 如果还没有连接，系统会要求你登录到电子邮件帐户。 逻辑应用创建到电子邮件帐户的连接。
        
    你需要允许逻辑应用访问电子邮件信息。
    
    ![Azure - 逻辑应用 - 访问通知](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png) 
        
21. 为用来通知收件人已关闭可选 VM 的电子邮件添加“收件人”、“主题”、“正文”文本。 使用 BudgetName 和 NotificationThresholdAmount 动态内容填充主题和正文字段。
    
    ![Azure - 逻辑应用 - 电子邮件详细信息](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png) 
 
### <a name="add-the-second-conditional-action"></a>添加第二个条件操作

使用条件语句检查阈值金额是否已达到或超过预算值的 100%。 如果已达到此阈值金额，则使用名为“Complete”的 Webhook 发送 HTTP POST。 此操作将关闭剩下的所有 VM。

1.  选择“新建步骤” > “添加条件”。
    
    ![Azure - 逻辑应用 - 添加操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png) 
        
2.  在“条件”框中，单击包含“选择值”的文本框，以显示可用值列表。
3.  单击列表顶部的“表达式”并在表达式编辑器中输入以下表达式：`float()`
4.  选择“动态内容”，将光标置于括号 () 内，然后从列表中选择“NotificationThresholdAmount”以填充整个表达式。 
    
    表达式如下：<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
        
5.  选择“确定”，以设置表达式。 
6.  在“条件”下拉框中选择“大于或等于”。
7.  在条件的“选择值”框中，输入 `1`。
    
    ![Azure - 逻辑应用 - 设置条件值](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png) 
        
8.  在“如果为 true”框中，选择“添加操作”。 将添加一个可关闭所有剩余 VM 的 HTTP POST 操作。
    
    ![Azure - 逻辑应用 - 添加操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png) 
    
9.  输入“HTTP”以搜索 HTTP 操作，然后选择“HTTP - HTTP”操作。
10. 选择“Post”作为“方法”值。
11. 对于之前在本教程中创建的名为“Complete”的 Webhook，请输入其 URL 作为 Uri 值。
    
    ![Azure - 逻辑应用 - 添加操作](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png) 
        
12. 在“如果为 true”框中，选择“添加操作”。 将添加一个电子邮件操作，该操作会发送一封电子邮件，通知收件人已关闭剩余的 VM。
13. 搜索“发送电子邮件”，然后根据所用的电子邮件服务选择“发送电子邮件”操作。
14. 为用来通知收件人已关闭可选 VM 的电子邮件添加“收件人”、“主题”、“正文”文本。 使用 BudgetName 和 NotificationThresholdAmount 动态内容填充主题和正文字段。
    
    ![Azure - 逻辑应用 - 发送电子邮件详细信息](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png) 
        
15. 单击“逻辑应用设计器”边栏选项卡顶部的“保存”。

### <a name="logic-app-summary"></a>逻辑应用摘要

完成后，逻辑应用如以下示例所示。 在最基本的方案（无需任何基于阈值的业务流程）中，可直接从 Monitor 调用自动化脚本，并跳过“逻辑应用”步骤。

   ![Azure - 逻辑应用 - 完整视图](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png) 
 
保存逻辑应用后，会生成可以调用的 URL。 在本教程的下一节中，会用到此 URL。 

## <a name="create-an-azure-monitor-action-group"></a>创建 Azure Monitor 操作组

操作组是用户定义的通知首选项的集合。 触发警报时，会向特定操作发出通知，让其接收警报。 Azure 警报会根据特定条件主动发出通知，并提供执行操作的机会。 警报可使用来自多个源（包括指标和日志）的数据。

操作组是将与预算集成的唯一终结点。 可在多个通道中设置通知，但在本方案中，将重点关注之前在本教程中创建的逻辑应用。 

### <a name="create-an-action-group-in-azure-monitor"></a>在 Azure Monitor 中创建操作组

创建操作组时，将指向之前在本教程中创建的逻辑应用。 

1.  如果尚未登录 [Azure 门户](https://portal.azure.com/)，请登录并选择“所有服务” > “Monitor”。 
2.  从“设置”部分选择“操作组”。
3.  从“操作组”边栏选项卡中选择“添加操作组”。
4.  添加并验证以下项：
    - 操作组名称
    - 短名称
    - 订阅
    - 资源组
    
    ![Azure - 逻辑应用 - 添加操作组](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png) 
        
5.  在“添加操作组”窗格中，添加 LogicApp 操作。 对“Budget-BudgetLA”操作进行命名。 在“逻辑应用”窗格中，选择“订阅”和“资源组”。 然后，选择之前在本教程中创建的“逻辑应用”。
6.  单击“确定”，以设置逻辑应用。 然后，“添加操作组”窗格中选择“确定”，以创建操作组。
    
你已完成有效安排预算所需的所有支持组件。 现在需要做的是创建预算，并将其配置为使用创建的操作组。

## <a name="create-the-azure-budget"></a>创建 Azure 预算 

在 Azure 中，“预算”目前还没有门户体验。 但可调用 REST API、Powershell cmdlet 或使用 CLI。 以下过程使用 REST API。 在调用 REST API 之前，需要授权令牌。 若要创建授权令牌，可使用 [ARMClient](https://github.com/projectkudu/ARMClient) 项目。 通过 ARMClient，可向 Azure 资源管理器进行身份验证，并获取令牌来调用 API。 

### <a name="create-an-authentication-token"></a>创建身份验证令牌

1.  导航到 GitHub 上的 [ARMClient](https://github.com/projectkudu/ARMClient) 项目。
2.  克隆存储库以获取本地副本。
3.  在 Visual Studio 中打开该项目并生成它。
4.  成功生成后，可执行文件应位于 \bin\debug 文件夹中。
5.  运行 ARMClient。 打开命令提示符并从项目根目录导航到 \bin\debug文件夹。
6.  若要登录并进行身份验证，请在命令提示符处输入以下命令：<br>
    `ARMClient login prod`
7.  从输出中复制“订阅 GUID”。
8.  若要将授权令牌复制到剪贴板，请在命令提示符处输入以下命令，不过请务必使用上述步骤中复制的订阅 ID： <br>
    `ARMClient token <subscription GUID from previous step>`
    
    完成上述步骤后，你将看到以下信息：<br>
    **令牌已成功复制到剪贴板。**
9.  保存令牌以供本教程下一部分中的步骤使用。

### <a name="create-the-budget"></a>创建预算

接下来，将通过调用 Azure Consumption REST API 来配置 Postman，以创建预算。 Postman 是一个 API 开发环境。 需将环境和集合文件导入 Postman 中。 集合包含调用 Azure Consumption REST API 的 HTTP 请求的分组定义。 环境文件包含集合使用的变量。

1.  下载并安装 [Postman REST 客户端](https://www.getpostman.com/)，以执行 REST API。
2.  在 Postman 中，创建新请求。
    
    ![Postman - 创建新请求](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png) 
        
3.  将新请求另存为集合，使新请求中不包含任何内容。
    
    ![Postman - 保存新请求](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png) 
        
4.  将请求从 `Get` 更改为 `Put` 操作。
5.  将 `{subscriptionId}` 替换为在本教程上一节中使用的“订阅 ID”，以此修改以下 URL。 另外，请修改 URL，以将“SampleBudget”添加为 `{budgetName}` 的值：`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  在 Postman 中选择“标头”选项卡。
7.  添加名为“授权”的新密钥。
8.  将值设置为使用最后一节末尾的 ArmClient 创建的令牌。 
9.  在 Postman 中选择“正文”选项卡。 
10. 选择“原始”按钮选项。
11. 在文本框中，粘贴以下预算定义示例，但必须将 subscriptionid、budgetname 和 actiongroupname 参数替换为你的订阅 ID、预算的唯一名称，以及在 URL 和请求正文中创建的操作组名称：
    
    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                }, 
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. 按“发送”，以发送请求。

现在你已经拥有调用[预算 API](https://docs.microsoft.com/rest/api/consumption/budgets) 所需的所有信息。 预算 API 引用包含关于特定请求的其他详细信息，包括以下内容：
    - **budgetName** - 支持多个预算。  预算名称必须是唯一的。
    - **类别** - 必须是“成本”或“使用情况”。 API 支持成本和使用情况预算。
    - **timeGrain** - 每月、每季度或每年的预算。 周期结束时重置金额。
    - **筛选器** - 可通过筛选器将预算缩小到所选范围内一组特定资源。 例如，筛选器可能是订阅级别预算的资源组集合。
    - **通知** - 确定通知详细信息和阈值。 可设置多个阈值并提供电子邮件地址或操作组以接收通知。

## <a name="summary"></a>摘要

本教程介绍了以下内容：
- 如何创建 Azure 自动化 Runbook 以停止 VM。
- 如何创建将根据预算阈值触发的 Azure 逻辑应用并调用包含正确参数的相关 runbook。
- 如何创建 Azure Monitor 操作组并将其配置为在达到预算阈值时触发 Azure 逻辑应用。
- 如何创建具有所需阈值的 Azure 预算，并将其绑定到操作组。

现在你已为订阅提供一个功能齐全的预算，它可以在达到配置的预算阈值时关闭 VM。 

## <a name="next-steps"></a>后续步骤

- 有关 Azure 计费方案的详细信息，请参阅[计费和成本管理自动化方案](billing-cost-management-automation-scenarios.md)。

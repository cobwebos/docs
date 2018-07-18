---
title: 以组或集合的形式对消息进行批处理 - Azure 逻辑应用 | Microsoft Docs
description: 在逻辑应用中发送和接收消息以进行批处理
keywords: 批, 批处理
author: jonfancey
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: 2815ce7fe0e10aadb60eaa77b58e5395fb5c98d8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298009"
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>在逻辑应用中对消息进行进行发送、接收和批处理

若要将消息以组的形式一起进行处理，可以将数据项或消息发送到批，然后以批的形式处理这些项。 如果要确保数据项以特定方式进行分组，并且一起进行处理，则此方法非常有用。 

可以使用**批**触发器创建以批形式接收各项的逻辑应用。 随后可以使用**批**操作创建将各项发送到批的逻辑应用。

本主题演示如何通过执行以下这些任务来构建批处理解决方案： 

* [创建以批形式接收和收集各项的逻辑应用](#batch-receiver)。 此“批接收方”逻辑应用指定在接收方逻辑应用发布和处理各项之前要满足的批名称和发布条件。 

* [创建将各项发送到批的逻辑应用](#batch-sender)。 此“批发送方”逻辑应用指定要向何处发送各项，这必须是现有批接收方逻辑应用。 还可以指定唯一键（如客户编号），以基于该键将目标批“分区”（或划分）为子集。 这样，具有该键的所有项都会一起进行收集和处理。 

## <a name="requirements"></a>要求

若要按照此示例进行操作，需要以下这些项：

* Azure 订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 也可以[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识 

* 具有任何 [Azure 逻辑应用支持的电子邮件提供商](../connectors/apis-list.md)的电子邮件帐户

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>创建以批形式接收消息的逻辑应用

必须首先使用**批**触发器创建“批接收方”逻辑应用，然后才能向批发送消息。 这样，便可以在创建发送方逻辑应用时选择此接收方逻辑应用。 对于接收方，需指定批名称、发布条件和其他设置。 

发送方逻辑应用需要知道要向何处发送各项，而接收方逻辑应用无需知道有关发送方的任何信息。

1. 在 [Azure 门户](https://portal.azure.com)中，创建具有以下名称的逻辑应用：“BatchReceiver” 

2. 在逻辑应用设计器中，添加**批**触发器，这会启动逻辑应用工作流。 在搜索框中，输入“批”作为筛选器。 选择此触发器：**批 – 批处理消息**

   ![添加批触发器](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. 为批提供名称，然后为发布批指定条件，例如：

   * **批名称**：用于标识批的名称，在此示例中是“TestBatch”。
   * **发布条件**：批发布条件，可以基于消息计数或/和计划。
   
     ![提供批触发器详细信息](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **消息计数**：在发布进行处理之前以批形式保留的消息数，在此示例中是“5”。

     ![提供批触发器详细信息](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **计划**：用于处理的批发布计划，本示例中为“每 5 分钟”。

     ![提供批触发器详细信息](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. 添加另一个在批触发器激发时发送电子邮件的操作。 每当批包含 5 个项或经过 5 分钟后，逻辑应用便会发送电子邮件。

   1. 在批触发器下，选择“+ 新步骤” > “添加操作”。

   2. 在搜索框中，输入“电子邮件”作为筛选器。
   基于电子邮件提供商，选择电子邮件连接器。
   
      例如，如果具有工作或学校帐户，则选择 Office 365 Outlook 连接器。 
      如果具有 Gmail 帐户，则选择 Gmail 连接器。

   3. 为连接器选择此操作：{电子邮件提供商} - 发送电子邮件

      例如：

      ![为电子邮件提供商选择“发送电子邮件”操作](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. 如果以前没有为电子邮件提供商创建连接，请在出现提示时提供电子邮件凭据进行身份验证。 详细了解[对电子邮件凭据进行身份验证](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

6. 为刚添加的操作设置属性。

   * 在“收件人”框中，输入收件人的电子邮件地址。 
   为进行测试，可以使用自己的电子邮件地址。

   * 在“主题”框中，当“动态内容”列表出现时，选择“分区名称”字段。

     ![从“动态内容”列表中，选择“分区名称”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     在后面的部分中，可以指定唯一分区键，它将目标批划分为可以向其发送消息的逻辑集。 
     每个集都具有发送方逻辑应用生成的唯一编号。 
     此功能使你可以使用具有多个子集的单个批，并且使用你提供的名称定义每个子集。

   * 在“正文”框中，当“动态内容”列表出现时，选择“消息 ID”字段。

     ![对于“正文”，选择“消息 ID”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     因为发送电子邮件操作的输入是数组，所以设计器会自动围绕“发送电子邮件”操作添加 **For each** 循环。 
     此循环对批中的每个项执行内部操作。 
     因此，在批触发器设置为五个项的情况下，每当触发器激发时，你都会收到五封电子邮件。

7.  现在已创建了批接收方逻辑应用，请保存逻辑应用。

    ![保存逻辑应用](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > 分区的限制为 5,000 条消息或 80 MB。 如果满足上述任一条件，可能会释放批，即使不满足用户定义的条件也是如此。


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>创建将消息发送到批的逻辑应用

现在创建一个或多个逻辑应用，以将各项发送到接收方逻辑应用定义的批。 对于发送方，可指定接收方逻辑应用以及批名称、消息内容和任何其他设置。 可以选择提供唯一分区键以将批划分为子集，从而收集具有该键的项。

发送方逻辑应用需要知道要向何处发送各项，而接收方逻辑应用无需知道有关发送方的任何信息。

1. 创建具有以下名称的另一个逻辑应用：“BatchSender”

   1. 在搜索框中，输入“定期”作为筛选器。 
   选择此触发器：**日程安排 - 定期**

      ![添加“日程安排 - 定期”触发器](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. 设置频率和间隔以便每分钟运行一次发送方逻辑应用。

      ![为定期触发器设置频率和间隔](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. 添加用于将消息发送到批的新步骤。

   1. 在定期触发器下，选择“+ 新步骤” > “添加操作”。

   2. 在搜索框中，输入“批”作为筛选器。 

   3. 选择此操作：**将消息发送到批 – 选择具有批触发器的逻辑应用工作流**

      ![选择“将消息发送到批”](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. 现在选择以前创建的“BatchReceiver”逻辑应用，它现在显示为一个操作。

      ![选择“批接收方”逻辑应用](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > 此列表还显示具有批触发器的任何其他逻辑应用。

3. 设置批属性。

   * **批名称**：接收方逻辑应用定义的批名称，在此示例中是“TestBatch”并在运行时进行验证。

     > [!IMPORTANT]
     > 确保不要更改批名称，这必须与接收方逻辑应用指定的批名称匹配。
     > 更改批名称会导致发送方逻辑应用失败。

   * **消息内容**：要发送的消息内容。 
   对于此示例，添加以下表达式，它将当前日期和时间插入到发送到批的消息内容中：

     1. 当“动态内容”列表出现时，选择“表达式”。 
     2. 输入表达式 **utcnow()**，然后选择“确定”。 

        ![在“消息内容”中，选择“表达式”。 输入“utcnow()”。](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. 现在为批设置分区。 在“BatchReceiver”操作中，选择“显示高级选项”。

   * **分区名称**：要用于划分目标批的可选唯一分区键。 对于此示例，添加一个生成介于一与五之间的随机数的表达式。
   
     1. 当“动态内容”列表出现时，选择“表达式”。
     2. 输入此表达式：**rand(1,6)**

        ![为目标批设置分区](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        此 **rand** 函数生成介于一与五之间的数字。 
        因此会将此批划分为五个带编号的分区（由此表达式动态设置）。

   * **消息 ID**：可选消息标识符，在为空时是生成的 GUID。 
   对于此示例，将此框保留为空白。

5. 保存逻辑应用。 发送方逻辑应用现在类似于此示例：

   ![保存发送方逻辑应用](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>测试逻辑应用

若要测试批处理解决方案，请让逻辑应用运行几分钟。 很快，你会开始收到五封一组的电子邮件，它们具有相同的分区键。

BatchSender 逻辑应用每分钟运行一次，生成介于一与五之间的随机数，并使用此生成的数字作为向其发送消息的目标批的分区键。 每当批包含具有相同分区键的五个项时，BatchReceiver 逻辑应用都会激发并为每个消息发送邮件。

> [!IMPORTANT]
> 完成测试之后，确保禁用 BatchSender 逻辑应用以停止发送消息，避免收件箱过载。

## <a name="next-steps"></a>后续步骤

* [使用 JSON 构建逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)
* [使用 Azure 逻辑应用和 Azure Functions 在 Visual Studio 中构建无服务器应用](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [逻辑应用的异常处理和错误日志记录](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

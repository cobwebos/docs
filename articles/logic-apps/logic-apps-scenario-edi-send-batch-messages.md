---
title: 以组或集合的形式对 EDI 消息进行批处理 - Azure 逻辑应用 | Microsoft Docs
description: 在逻辑应用中发送 EDI 消息以进行批处理
keywords: batch, 批处理, 批处理编码
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: fb15688968cb29039fc669ed6b8685ba64df9e81
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432127"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>将 X12 消息以批的形式发送到参与方

在企业到企业 (B2B) 方案中，合作伙伴通常以组或批的形式交换消息。 若要将消息以组或批的形式发送到参与方，可以创建包含多个项的批，然后使用 X12 批处理操作，将这些项作为一个批进行处理。


与其他消息一样，X12 消息的批处理使用批处理触发器和操作。 另外，对于 X12，批会先经历 X12 编码步骤，然后转到合作伙伴或其他任何目标。 有关批处理触发器和操作的详细信息，请参阅[批处理消息](logic-apps-batch-process-send-receive-messages.md)。

本主题介绍如何通过执行以下任务，将 X12 消息作为批进行处理：
* [创建可接收项的逻辑应用并创建批](#receiver)。 此“接收方”逻辑应用执行以下操作：
 
   * 指定批名称，以及将项发布为批之前所要满足的发布条件。

   * 使用指定的 X12 协议或合作伙伴标识处理或编码批中的项。

* [创建将各项发送到批的逻辑应用](#sender)。 此“发送方”逻辑应用指定要将项发送到何处进行批处理（此目标位置必须是现有的接收方逻辑应用）。


## <a name="prerequisites"></a>先决条件

若要按照此示例进行操作，需要以下这些项：

* Azure 订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 也可以[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 已定义的、与 Azure 订阅关联的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)

* 已在集成帐户中至少定义了两个[合作伙伴](logic-apps-enterprise-integration-partners.md)。 确保每个合作伙伴在合作伙伴属性中使用 X12（标准承运人字母代码）限定符作为企业标识。

* 已在集成帐户中定义了 [X12 协议](logic-apps-enterprise-integration-x12.md)

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>创建可接收 X12 消息的逻辑应用并创建批

必须先使用“批处理”触发器创建“接收方”逻辑应用，然后才能向批发送消息。 这样，便可以在创建发送方逻辑应用时选择此接收方逻辑应用。 对于接收方，需指定批名称、发布条件、X12 协议和其他设置。 


1. 在 [Azure 门户](https://portal.azure.com)中，创建名为“BatchX12Messages”的逻辑应用。

1. 在逻辑应用设计器中，添加**批**触发器，这会启动逻辑应用工作流。 在搜索框中，输入“批”作为筛选器。 选择此触发器：**批 – 批处理消息**

   ![添加批触发器](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

1. 为批提供名称，然后为发布批指定条件，例如：

   * **批名称**：用于标识批的名称，在此示例中是“TestBatch”。

   * **发布条件**：批发布条件，可以基于消息计数或/和计划。
   
     ![提供批触发器详细信息](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **消息计数**：在发布进行处理之前以批形式保留的消息数，在此示例中是“5”。

     ![提供批触发器详细信息](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **计划**：要处理的批发布计划，本示例中为“每 10 分钟”。

     ![提供批触发器详细信息](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


1. 添加用于编码已分组或批处理的消息的另一个操作，并创建 X12 批处理消息。 

   a. 选择“+ 新建步骤” > “添加操作”。

   b. 在搜索框中，输入“X12 批”作为筛选器，为“X12 - 批编码”选择一项操作。 与“X12 编码”连接器一样，批编码操作有多个变体。 可以选择其中任何一项。

   ![选择“X12 批编码”操作](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
1. 为刚添加的操作设置属性。

   * 在“X12 协议名称”框中，从下拉列表中选择协议。 如果列表为空，请确保与集成帐户建立了连接。

   * 在“批名称”框中，从动态内容列表中选择“批名称”字段。
   
   * 在“分区名称”框中，从动态内容列表中选择“分区名称”字段。

   * 在“项”框中，从动态内容列表中选择“批处理项”。

   ![批编码操作详细信息](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

1. 出于测试目的，请添加用于将批处理消息发送到[请求 Bin 服务](https://requestbin.fullcontact.com/)的 HTTP 操作。 

   1. 在搜索框中，输入“HTTP”作为筛选器。 选择此操作：“HTTP - HTTP”
    
      ![选择“HTTP”操作](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   1. 在“方法”列表中，选择“POST”。 在“URI”框中，生成请求 bin 的 URI，并输入该 URI。 在“正文”框中，如果动态列表已打开，请选择“按协议名称批量编码”部分下面的“正文字段”。 如果未显示“正文”，请选择“按协议名称批量编码”旁边的“查看更多”。

      ![提供 HTTP 操作详细信息](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

1.  现已创建接收方逻辑应用，请保存该逻辑应用。

    ![保存逻辑应用](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > 分区的限制为 5,000 条消息或 80 MB。 如果满足上述任一条件，可能会释放批，即使不满足用户定义的条件也是如此。

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>创建可将 X12 消息发送到批的逻辑应用

现在创建一个或多个逻辑应用，以将各项发送到接收方逻辑应用定义的批。 对于发送方，可指定接收方逻辑应用以及批名称、消息内容和任何其他设置。 可以选择提供唯一分区键以将批划分为子集，从而收集具有该键的项。

发送方逻辑应用需知道要向何处发送各项，而接收方逻辑应用无需知道有关发送方的任何信息。


1. 创建名为“X12MessageSender”的另一个逻辑应用。 将以下触发器添加到该逻辑应用：“请求/响应 - 请求” 
   
   ![添加“请求”触发器](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

1. 添加用于将消息发送到批的新步骤。

   1. 选择“+ 新建步骤” > “添加操作”。

   1. 在搜索框中，输入“批”作为筛选器。 

1. 选择此操作：**将消息发送到批 – 选择具有批触发器的逻辑应用工作流**

   ![选择“将消息发送到批”](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

1. 选择前面创建的“BatchX12Messages”逻辑应用，它现在显示为一个操作。

   ![选择“批接收方”逻辑应用](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > 此列表还显示具有批触发器的任何其他逻辑应用。

1. 设置批属性。

   * **批名称**：接收方逻辑应用定义的批名称，在此示例中是“TestBatch”并在运行时进行验证。

     > [!IMPORTANT]
     > 确保不要更改批名称，这必须与接收方逻辑应用指定的批名称匹配。
     > 更改批名称会导致发送方逻辑应用失败。

   * **消息内容**：要发送到批的消息内容
   
   ![设置批属性](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

1. 保存逻辑应用。 发送方逻辑应用现在类似于此示例：

   ![保存发送方逻辑应用](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>测试逻辑应用

若要测试批处理解决方案，请通过 [Postman](https://www.getpostman.com/postman) 或类似工具将 X12 消息发布到发送方逻辑应用。 请求 bin 中应该很快就能开始收到 X12 消息，这些消息要么采用 5 项一批的形式，要么每隔 10 分钟发送一次 - 全都使用相同的分区键。

## <a name="next-steps"></a>后续步骤

* [以批的形式处理消息](logic-apps-batch-process-send-receive-messages.md) 
* [使用 Azure 逻辑应用和 Azure Functions 在 Visual Studio 中构建无服务器应用](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [逻辑应用的异常处理和错误日志记录](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

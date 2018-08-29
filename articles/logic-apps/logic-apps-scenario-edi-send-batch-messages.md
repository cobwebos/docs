---
title: 以组或集合的形式对 EDI 消息进行批处理 - Azure 逻辑应用 | Microsoft Docs
description: 在逻辑应用中发送 EDI 消息以进行批处理
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
manager: jeconnoc
ms.topic: article
ms.date: 08/19/2018
ms.reviewer: estfan, LADocs
ms.openlocfilehash: 77965e20e7d42d12b34bcb2f7cc6c8680ba34b3a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "42141049"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>使用 Azure 逻辑应用将 EDI 消息以批的形式发送到参与方

在企业到企业 (B2B) 方案中，合作伙伴通常以组或批的形式交换消息。 使用逻辑应用生成批处理解决方案时，可将消息发送到参与方，然后以批的形式统一处理这些消息。 本文以 X12 为例，介绍如何通过创建“批发送方”逻辑应用和“批接收方”逻辑应用来批处理 EDI 消息。 

批处理 X12 消息的方式如同处理其他消息：使用批处理触发器将消息收集到某个批中，然后使用批处理操作将消息发送到批。 此外，在消息转到参与方或其他目标之前，X12 批处理包含一个 X12 编码步骤。 有关批处理触发器和操作的详细信息，请参阅[批处理消息](../logic-apps/logic-apps-batch-process-send-receive-messages.md)。

在本文中，我们将通过遵循以下特定顺序在同一个 Azure 订阅和 Azure 区域中创建两个逻辑应用，来生成批处理解决方案：

* [“批接收方”](#receiver)逻辑应用：接受消息并将其收集到批中，直到满足发布和处理这些消息的指定条件。 在此方案中，批接收方还会使用指定的 X12 协议或合作伙伴标识来编码批中的消息。

  请务必先创建批接收方，然后才能在创建批发送方时选择批处理目标。

* [“批发送方”](#sender)逻辑应用：将消息发送到前面创建的批接收方。 

请确保批接收方和批发送方共享同一个 Azure 订阅和 Azure 区域。 否则，在创建批发送方时无法选择批接收方，因为它们相互不可见。

## <a name="prerequisites"></a>先决条件

若要按照此示例进行操作，需要以下这些项：

* Azure 订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 或者[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 与 Azure 订阅相关联的并链接到逻辑应用的现有[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 集成帐户中至少有两个现有的[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。 每个合作伙伴在合作伙伴属性中使用 X12（标准运营商字母代码）限定符作为企业标识。

* 集成帐户中有一个现有的 [X12 协议](../logic-apps/logic-apps-enterprise-integration-x12.md)

* 若要使用 Visual Studio 而不是 Azure 门户，请确保[设置 Visual Studio 以使用逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>创建 X12 批接收方

在将消息发送到某个批之前，该批必须存在且充当这些消息要发送到的目标。 因此，首先必须创建可以通过“批处理”触发器启动的“批接收方”逻辑应用。 这样，在创建“批发送方”逻辑应用时，便可以选择批接收方逻辑应用。 批接收方会持续收集消息，直到满足发布和处理这些消息的指定条件。 尽管批接收方不需要知道有关批发送方的任何信息，但批发送方必须知道要将消息发送到的目标。 

对于此批接收方，需指定批模式、名称、发布条件、X12 协议和其他设置。 

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 中，创建名为“BatchX12Messages”的逻辑应用

2. [将逻辑应用链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

3. 在逻辑应用设计器中，添加**批**触发器，这会启动逻辑应用工作流。 在搜索框中，输入“批”作为筛选器。 选择此触发器：“批处理消息”

   ![添加批触发器](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. 设置批接收方属性： 

   | 属性 | 值 | 说明 | 
   |----------|-------|-------|
   | **批处理模式** | 内联 |  |  
   | **批名称** | TestBatch | 仅适用于“内联”批处理模式 | 
   | **发布条件** | 基于消息计数、基于计划 | 仅适用于“内联”批处理模式 | 
   | **消息计数** | 10 | 仅适用于“基于消息计数”发布条件 | 
   | 间隔 | 10 | 仅适用于“基于计划”发布条件 | 
   | **频率** | minute | 仅适用于“基于计划”发布条件 | 
   ||| 

   ![提供批处理触发器详细信息](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > 本示例不会设置批的分区，因此每个批次使用相同的分区键。 若要详细了解分区，请参阅[批处理消息](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)。

5. 现在，添加一个用于编码每个批的操作： 

   1. 在批处理触发器下，选择“新建步骤”。

   2. 在搜索框中，输入“X12 批”作为筛选器，并选择以下操作（任何版本）：“批编码 <版本> - X12” 

      ![选择“X12 批编码”操作](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. 如果事先未连接到集成帐户，现在请创建连接。 提供连接的名称，选择集成帐户，然后选择“创建”。

      ![在批编码器与集成帐户之间创建连接](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. 设置批编码器操作的以下属性：

      | 属性 | Description |
      |----------|-------------|
      | **X12 协议的名称** | 打开列表，并选择现有的协议。 <p>如果该列表为空，请确保[将逻辑应用链接到包含所需协议的集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。 | 
      | **BatchName** | 在此框中单击，显示动态内容列表后，选择“批名称”标记。 | 
      | **PartitionName** | 在此框中单击，显示动态内容列表后，选择“分区名称”标记。 | 
      | **项** | 关闭项详细信息，然后在此框中单击。 显示动态内容列表后，选择“已批处理的项”标记。 | 
      ||| 

      ![“批编码”操作详细信息](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      对于“项”框：

      ![“批编码”操作项](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. 保存逻辑应用。 

7. 如果使用 Visual Studio，请确保[将批接收方逻辑应用部署到 Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)。 否则，在创建批发送方时无法选择批接收方。

### <a name="test-your-logic-app"></a>测试逻辑应用

为确保批接收方按预期方式工作，可以添加一个 HTTP 操作用于测试，并将批处理的消息发送到[请求 Bin 服务](https://requestbin.fullcontact.com/)。 

1. 在 X12 编码操作下，选择“新建步骤”。 

2. 在搜索框中，输入“http”作为筛选器。 选择此操作：“HTTP - HTTP”
    
   ![选择“HTTP”操作](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. 为 HTTP 操作设置属性：

   | 属性 | Description | 
   |----------|-------------|
   | **方法** | 在此列表中选择“POST”。 | 
   | **Uri** | 生成请求 bin 的 URI，然后在此框中输入该 URI。 | 
   | **正文** | 在此框中单击，动态内容列表打开后，选择“按协议名称进行批编码”部分显示的“正文”标记。 <p>如果未显示“正文”标记，请在“按协议名称进行批编码”旁边选择“查看更多”。 | 
   ||| 

   ![提供 HTTP 操作详细信息](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. 保存逻辑应用。 

   批接收方逻辑应用如以下示例所示： 

   ![保存批接收方逻辑应用](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>创建 X12 批发送方

现在创建一个或逻辑应用，用于将消息发送到批接收方逻辑应用。 在每个批发送方中，指定批接收方逻辑应用和批名称、消息内容及其他任何设置。 可以选择提供唯一分区键以将批划分为子集，从而收集具有该键的消息。 

* 确保已[创建批接收方](#receiver)，以便在创建批发送方时，可以选择现有的批接收方作为目标批。 尽管批接收方不需要知道有关批发送方的任何信息，但批发送方必须知道要将消息发送到何处。 

* 请确保批接收方和批发送方共享同一个 Azure 区域和 Azure 订阅。 否则，在创建批发送方时无法选择批接收方，因为它们相互不可见。

1. 创建名为“SendX12MessagesToBatch”的另一个逻辑应用 

2. 在搜索框中，输入“收到 http 请求时”作为筛选器。 选择此触发器：“收到 HTTP 请求时” 
   
   ![添加“请求”触发器](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. 添加用于将消息发送到批的操作。

   1. 在 HTTP 请求操作下，选择“新建步骤”。

   2. 在搜索框中，输入“批”作为筛选器。 
   选择“操作”列表，然后选择此操作：“选择具有批处理触发器的逻辑应用工作流 - 将消息发送到批”

      ![选择“选择具有批处理触发器的逻辑应用工作流”](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. 选择前面创建的“BatchX12Messages”逻辑应用。

      ![选择“批接收方”逻辑应用](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. 选择此操作：“Batch_messages - <批接收方>”

      ![选择“Batch_messages”操作](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. 设置批发送方的属性。

   | 属性 | Description | 
   |----------|-------------| 
   | **批名称** | 接收方逻辑应用定义的批名称，在本示例中为“TestBatch” <p>**重要说明**：批名称在运行时将接受验证，必须与接收方逻辑应用指定的名称相匹配。 更改批名称会导致批发送方失败。 | 
   | **消息内容** | 要发送的消息内容，在本示例中为“正文”标记 | 
   ||| 
   
   ![设置批属性](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. 保存逻辑应用。 

   批发送方逻辑应用如以下示例所示：

   ![保存批发送方逻辑应用](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>测试逻辑应用

若要测试批处理解决方案，请通过 [Postman](https://www.getpostman.com/postman) 或类似工具将 X12 消息发布到批发送方逻辑应用。 很快，请求 bin 中就会每隔 10 分钟或者以 10 个一批的形式接收 X12 消息，所有这些消息具有相同的分区键。

## <a name="next-steps"></a>后续步骤

* [以批的形式处理消息](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 

---
title: 将消息作为一个组进行批处理
description: 通过在 Azure 逻辑应用中使用批处理，在工作流之间以组的形式发送和接收消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87458188"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>在 Azure 逻辑应用中发送、接收和批处理消息

若要以特定方式将消息以组的形式一起发送和处理，可以创建一个批处理解决方案。 此解决方案将消息收集到一个 *批处理* 中，并等待直到满足指定的条件，然后再释放并处理批处理消息。 批处理可以减少逻辑应用处理消息的频率。

本文介绍如何通过在同一 Azure 订阅和 Azure 区域中创建两个逻辑应用来构建批处理解决方案，如下所示：

1. [“批接收方”](#batch-receiver)逻辑应用：接受消息并将其收集到批中，直到满足发布和处理这些消息的指定条件。 请确保首先创建此批处理接收器，以便以后在创建批处理发送方时选择该批目标。

1. 一个或多个[“批发送方”](#batch-sender)逻辑应用：将消息发送到前面创建的批接收方。

   还可以指定唯一键（如客户编号），以基于该键将目标批分区或分割为逻辑子集。 这样，接收方应用便可以收集具有相同键的所有项，并统一处理它们。

Batch 接收方和批处理发送方需要共享同一 Azure 订阅 *和* azure 区域。 否则，在创建批发送方时无法选择批接收方，因为它们相互不可见。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 或者[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 具有任何 [Azure 逻辑应用支持的电子邮件提供商](../connectors/apis-list.md)的电子邮件帐户

  > [!IMPORTANT]
  > 如果要使用 Gmail 连接器，则只有 G-Suite 商业帐户可以在逻辑应用中不受限制地使用此连接器。 如果有 Gmail 用户帐户，则只能将此连接器与 Google 批准的特定服务一起使用，也可以[创建用于通过 Gmail 连接器进行身份验证的 Google 客户端应用](/connectors/gmail/#authentication-and-bring-your-own-application)。 有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 若要使用 Visual Studio 而不是 Azure 门户，请确保 [设置 Visual studio 以使用逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>创建批接收方

在将消息发送到某个批之前，该批必须存在且充当这些消息要发送到的目标。 因此，首先必须创建可以通过“批处理”触发器启动的“批接收方”逻辑应用。 这样，在创建“批发送方”逻辑应用时，便可以选择批接收方逻辑应用。 批接收方会持续收集消息，直到满足发布和处理这些消息的指定条件。 尽管批接收方不需要知道有关批发送方的任何信息，但批发送方必须知道要将消息发送到的目标。

1. 在 [Azure 门户](https://portal.azure.com) 或 Visual Studio 中，创建一个具有此名称的逻辑应用： `BatchReceiver`

1. 在逻辑应用设计器中，添加 **批处理** 触发器，该触发器启动逻辑应用工作流。 在搜索框中，输入 `batch` ，然后选择 "此触发器：**批处理消息**"

   ![添加“批处理消息”触发器](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. 为批接收方设置以下属性：

   | 属性 | 说明 |
   |----------|-------------|
   | **批处理模式** | - 内联：用于在批处理触发器中定义发布条件 <br>- 集成帐户：用于通过[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)定义多个发布条件配置。 使用集成帐户可在一个位置而不是独立的逻辑应用中维护这些配置。 |
   | **批名称** | 批的名称（在本示例中为“TestBatch”），仅适用于“内联”批处理模式 |
   | **发布条件** | 仅适用于“内联”批处理模式，选择在处理每个批之前所要满足的条件： <p>- **基于消息计数**：根据批收集的消息数发布批。 <br>- **基于大小**：根据批收集的所有消息的总大小（以字节为单位）发布批。 <br>- 计划：根据指定间隔和频率的定期计划发布批。 在高级选项中，还可以选择时区，并提供开始日期和时间。 <br>- **全选**：使用所有指定的条件。 |
   | **消息计数** | 要在批中收集的消息数，例如 10 条消息。 批的限制为 8,000 条消息。 |
   | **批大小** | 批中要收集的总大小（以字节为单位），例如10 MB。 批的大小上限是 80 MB。 |
   | **计划** | 发布批的间隔时间和频率，例如 10 分钟。 最小重复周期为 60 秒或 1 分钟。 分数分钟有效地向上舍入到 1 分钟。 若要指定时区或开始日期和时间，请打开 " **添加新参数** " 列表，然后选择相应的属性。 |
   |||

   > [!NOTE]
   >
   > 如果在触发器仍具有已批处理但未发送的消息时更改发布条件，则触发器将使用更新的发布条件来处理未发送的消息。

   此示例显示了所有条件，但是对于自己的测试，请仅尝试一个条件：

   ![提供批触发器详细信息](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. 现在，添加一个或多个操作用于处理每个批。

   本示例添加一个可在批处理触发器激发时发送电子邮件的操作。 当批包含 10 个消息、达到 10 MB 或者在 10 分钟过后，该触发器将运行并发送电子邮件。

   1. 在批处理触发器下，选择 " **新建步骤**"。

   1. 在搜索框中，输入 `send email` 作为筛选器。 基于电子邮件提供商，选择电子邮件连接器。

      例如，如果有工作或学校帐户（例如或），请 @fabrikam.com @fabrikam.onmicrosoft.com 选择 " **Microsoft 365 Outlook** 连接器"。 如果有个人帐户（如 @outlook.com 或 @hotmail.com ），请选择 **Outlook.com** 连接器。 此示例使用 Microsoft 365 Outlook 连接器。

   1. 为您的提供商选择 "发送电子邮件" 操作，例如：

      ![为电子邮件提供商选择“发送电子邮件”操作](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. 根据提示登录到电子邮件帐户。

1. 为添加的操作设置属性。

   * 在“收件人”框中，输入收件人的电子邮件地址。 为进行测试，可以使用自己的电子邮件地址。

   * 在 " **主题** " 框中，当 "动态内容" 列表出现时，选择 " **分区名称** " 字段。

     ![从“动态内容”列表中选择“分区名称”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     稍后，在批发送方中，可以指定唯一分区键，将目标批划分为可以在其中发送消息的逻辑子集。 每个集都具有批发送方逻辑应用生成的唯一编号。 此功能使你可以使用具有多个子集的单个批，并且使用你提供的名称定义每个子集。

     > [!IMPORTANT]
     > 分区的限制为 5,000 条消息或 80 MB。 如果满足任一条件，则逻辑应用可以发布批，即使不满足定义的发布条件。

   * 在 " **正文** " 框中，当 "动态内容" 列表出现时，选择 " **消息 Id** " 字段。

     逻辑应用设计器会自动为 "发送电子邮件" 操作添加一个 **for each** 循环，因为该操作将上一操作的输出视为集合，而不是批处理。

     ![对于“正文”，选择“消息 ID”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. 保存逻辑应用。 现已创建批接收方。

    ![保存逻辑应用](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > 如果你使用的是 Visual Studio，则在继续下一部分之前，请确保先[将 batch 接收器逻辑应用*部署*到 Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)。 否则，在创建批发送方时无法选择批接收方。

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>创建批发送方

现在创建一个或多个批发送方逻辑应用，用于将消息发送到批接收方逻辑应用。 在每个批发送方中，指定批接收方和批名称、消息内容及其他任何设置。 可以选择性地提供唯一分区键，以将批分割为子集，从而收集具有该键的消息。

* 请确保以前 [创建并部署了 batch 接收器](#batch-receiver) ，以便在创建批处理发送方时，可以选择现有批处理接收器作为目标批。 尽管批接收方不需要知道有关批发送方的任何信息，但批发送方必须知道要将消息发送到何处。

* 请确保 batch 接收方和批处理发送方共享同一 Azure 区域 *和* azure 订阅。 否则，在创建批发送方时无法选择批接收方，因为它们相互不可见。

1. 创建具有此名称的另一个逻辑应用： `BatchSender`

   1. 在搜索框中，输入 `recurrence` 作为筛选器。 从触发器列表中选择此触发器：**定期**

      ![添加重复触发器](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. 设置每分钟运行发送方逻辑应用的间隔和频率。

      ![设置定期触发器的频率和间隔](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. 添加用于将消息发送到批的新操作。

   1. 在 **定期** 触发器下，选择 " **新建步骤**"。

   1. 在搜索框中，输入 `batch` 作为筛选器，然后选择此操作： **使用批处理触发器选择逻辑应用工作流**

      ![选择“选择具有批处理触发器的逻辑应用工作流”](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      此时将显示一个列表，其中仅显示具有批处理触发器并与 batch 发送方逻辑应用位于同一 Azure 区域 *和* Azure 订阅中的逻辑应用。

   1. 从 "逻辑应用" 列表中，选择之前创建的批处理接收方逻辑应用。

      ![选择 batch 接收方逻辑应用](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > 如果你使用的是 Visual Studio，但未看到任何要选择的批处理接收器，请检查你以前创建的批处理接收器并将其部署到 Azure。 如果尚未这样做，请学习 [如何将 batch 接收器逻辑应用部署到 Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)。

   1. 从 "操作" 列表中，选择此操作： **Batch_messages *your-logic-app-name* > -<**

      ![选择以下操作：“Batch_messages - <your-logic-app>”](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. 设置批发送方的属性：

   | 属性 | 说明 |
   |----------|-------------|
   | **批名称** | 接收方逻辑应用定义的批名称， `TestBatch` 此示例中为 <p>**重要说明**：批名称在运行时将接受验证，必须与接收方逻辑应用指定的名称相匹配。 更改批名称会导致批发送方失败。 |
   | **消息内容** | 要发送的消息内容 |
   |||

   > [!NOTE]
   > **触发器名称**和**工作流**属性值将自动从所选逻辑应用填充。

   本示例将添加以下表达式，该表达式在要发送到批的消息内容中插入当前日期和时间：

   1. 在“消息内容”框中单击。

   1. 显示动态内容列表时，选择 " **表达式**"。

   1. 输入表达式 `utcnow()` ，然后选择 **"确定"**。

      ![在 "消息内容" 中，选择 "表达式"，输入 "utcnow ( # A1"，然后选择 "确定"。](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. 现在为批设置分区。 在 `BatchReceiver` 操作中，打开 " **添加新参数** " 列表，然后选择以下属性：

   | 属性 | 说明 |
   |----------|-------------|
   | **分区名称** | 可选的唯一分区键，用于将目标批分割为逻辑子集，并基于该键收集消息 |
   | **消息 ID** | 可选的消息标识符，为空时是系统生成的全局唯一标识符 (GUID) |
   |||

   对于本示例，请在“分区名称”框中添加一个表达式，用于生成介于 1 和 5 之间的随机数。 将“消息 ID”框留空。

   1. 在 " **分区名称** " 框中单击，以便显示 "动态内容" 列表。

   1. 在动态内容列表中，选择“表达式”。

   1. 输入表达式 `rand(1,6)` ，然后选择 **"确定"**。

      ![为目标批设置分区](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      此 **rand** 函数生成介于一与五之间的数字。 因此会将此批划分为五个带编号的分区（由此表达式动态设置）。

1. 保存逻辑应用。 发送方逻辑应用现在类似于此示例：

   ![保存发送方逻辑应用](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>测试逻辑应用

若要测试批处理解决方案，请让逻辑应用运行几分钟。 很快，你会开始收到五封一组的电子邮件，它们具有相同的分区键。

批发送方逻辑应用每分钟运行一次，生成介于 1 和 5 之间的随机数，并使用此生成的数字作为向其发送消息的目标批的分区键。 每当批中包含具有相同分区键的 5 个项时，批接收方逻辑应用都会激发并针对每个消息发送邮件。

> [!IMPORTANT]
> 完成测试后，请确保禁用 `BatchSender` 逻辑应用以停止发送消息，并避免重载收件箱。

## <a name="next-steps"></a>后续步骤

* [批处理和发送 EDI 消息](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [使用 JSON 构建逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)
* [使用 Azure 逻辑应用和 Azure Functions 在 Visual Studio 中构建无服务器应用](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [逻辑应用的异常处理和错误日志记录](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

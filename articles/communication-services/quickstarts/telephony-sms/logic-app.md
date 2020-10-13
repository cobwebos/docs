---
title: 快速入门 - 使用 Azure 通信服务在 Azure 逻辑应用中发送短信
titleSuffix: An Azure Communication Services quickstart
description: 本快速入门介绍如何使用 Azure 通信服务连接器在 Azure 逻辑应用工作流中发送短信。
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ed9af178c10d033806b05f73372a0642bb28cab4
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801627"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>快速入门：使用 Azure 通信服务在 Azure 逻辑应用中发送短信

借助 [Azure 通信服务短信](../../overview.md)连接器和 [Azure 逻辑应用](../../../logic-apps/logic-apps-overview.md)，可以创建自动化工作流或逻辑应用来发送短信。 本快速入门介绍如何自动发送短信以响应触发器事件，这是逻辑应用工作流的第一步。 触发器事件可以是传入电子邮件、定期计划、[Azure 事件网格](../../../event-grid/overview.md)资源事件或 [Azure 逻辑应用支持的任何其他触发器](/connectors/connector-reference/connector-reference-logicapps-connectors)。

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="屏幕截图显示了打开至逻辑应用设计器的 Azure 门户，以及一个示例逻辑应用，该应用使用 Azure 通信服务连接器的“发送短信”操作。":::

尽管本快速入门重点介绍如何使用连接器来响应触发器，但你也可以使用连接器来响应其他操作，这些是工作流中触发器之后的步骤。 如果不熟悉逻辑应用，请在开始之前查看[什么是 Azure 逻辑应用](../../../logic-apps/logic-apps-overview.md)。

> [!NOTE]
> 完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

## <a name="prerequisites"></a>先决条件

- 具备有效订阅的 Azure 帐户，或[免费创建一个 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 有效的 Azure 通信服务资源，或[创建一个通信服务资源](../create-communication-resource.md)。

- 有效的逻辑应用资源（逻辑应用），或[创建一个空白逻辑应用，但具有你要使用的触发器](../../../logic-apps/quickstart-create-first-logic-app-workflow.md)。 目前，Azure 通信服务短信连接器仅提供操作，因此逻辑应用至少需要一个触发器。

  本快速入门使用 [Office 365 Outlook 连接器](/connectors/office365/)提供的“新电子邮件到达时”触发器。

- 支持短信的电话号码，或[获取一个电话号码](./get-phone-number.md)。

## <a name="add-an-sms-action"></a>“添加短信”操作

若要使用 Azure 通信服务短信连接器添加“发送短信”操作，作为工作流中的新步骤，请在 [Azure 门户](https://portal.azure.com)中打开了逻辑应用工作流的逻辑应用设计器中执行以下步骤：

1. 在设计器中，在要添加新操作的步骤下，选择“新建步骤”。 或者，若要在步骤之间添加新操作，请将指针移到这些步骤之间的箭头上方，选择加号 ( **+** )，然后选择“添加操作”。

1. 在“选择操作”搜索框中，输入 `Azure Communication Services`。 从操作列表中选择“发送短信”。

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="屏幕截图显示了打开至逻辑应用设计器的 Azure 门户，以及一个示例逻辑应用，该应用使用 Azure 通信服务连接器的“发送短信”操作。":::

1. 现在，创建与通信服务资源的连接。

   1. 为连接提供一个名称。

   1. 选择 Azure 通信服务资源。

   1. 选择“创建”。

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="屏幕截图显示了打开至逻辑应用设计器的 Azure 门户，以及一个示例逻辑应用，该应用使用 Azure 通信服务连接器的“发送短信”操作。":::

1. 在“发送短信”操作中，提供以下信息： 

   * 源电话号码和目标电话号码。 为了进行测试，你可以使用自己的电话号码作为目标电话号码。

   * 你要发送的短信内容，例如“Hello from Logic Apps!”。

   下面显示了包含示例信息的“发送短信”操作：

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="屏幕截图显示了打开至逻辑应用设计器的 Azure 门户，以及一个示例逻辑应用，该应用使用 Azure 通信服务连接器的“发送短信”操作。":::

1. 完成后，请在设计器工具栏上选择“保存”。

接下来，运行逻辑应用进行测试。

## <a name="test-your-logic-app"></a>测试逻辑应用

若要手动启动逻辑应用，请在设计器工具栏中选择“运行”。 或者，可以等待逻辑应用触发。 无论哪种情况，逻辑应用都应将短信发送到指定的目标电话号码。 有关运行逻辑应用的详细信息，请参阅[如何运行逻辑应用](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>清理资源

若要删除通信服务订阅，请删除通信服务资源或资源组。 删除资源组同时也会删除该组中的所有其他资源。 有关详细信息，请参阅[如何清理通信服务资源](../create-communication-resource.md#clean-up-resources)。

若要清理逻辑应用工作流和相关资源，请参阅[如何清理逻辑应用资源](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure 逻辑应用和 Azure 通信服务发送短信。 若要了解详细信息，请继续订阅短信事件：

> [!div class="nextstepaction"]
> [订阅短信事件](./handle-sms-events.md)

有关 Azure 通信服务中的短信的详细信息，请参阅以下文章：

- [短信概念](../../concepts/telephony-sms/concepts.md)
- [规划电话和短信解决方案](../../concepts/telephony-sms/plan-solution.md)
- [短信 SDK](../../concepts/telephony-sms/sdk-features.md)

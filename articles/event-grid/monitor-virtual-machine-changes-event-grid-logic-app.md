---
title: "监视虚拟机更改 - Azure 事件网格和逻辑应用 | Microsoft 文档"
description: "通过使用 Azure 事件网格和逻辑应用检查虚拟机 (VM) 中的配置更改"
keywords: "逻辑应用、事件网格、虚拟机、VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 08/16/2017
ms.author: LADocs; estfan
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 4d4c16860dbec10162797a13c8f9f57106abd17f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/19/2017

---

# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>通过 Azure 事件网格和逻辑应用监视虚拟机更改

你可以在 Azure 资源或第三方资源中发生特定事件时，启动自动化[逻辑应用工作流](../logic-apps/logic-apps-what-are-logic-apps.md)。 这些资源可以将这些事件发布到 [Azure 事件网格](../event-grid/overview.md)。 然后，事件网格会将这些事件推送给具有队列、webhook 或[事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)作为终结点的订阅者。 作为订阅者，逻辑应用可以在运行自动化工作流以执行任务之前等待这些来自事件网格的事件 - 而无需编写任何代码。

例如，下面是发布者可以将通过 Azure 事件网格服务发送给订阅者的某些事件：

* 创建、读取、更新或删除资源。 例如，你可以监视可能在 Azure 订阅中产生费用并影响你账单的更改。 
* 从 Azure 订阅添加或删除某个人。
* 你的应用可执行特定的操作。
* 队列中显示新消息。

本教程将创建一个逻辑应用，监视对虚拟机的更改并就这些更改发送电子邮件。 当你为 Azure 资源创建具有事件订阅的逻辑应用时，事件会通过事件网格从该资源流向逻辑应用。 本教程将指导你完成构建此逻辑应用：

![概述 - 通过事件网格和逻辑应用监视虚拟机](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建通过事件网格监视事件的逻辑应用。
> * 添加一个专门检查虚拟机更改的条件。
> * 虚拟机更改时发送电子邮件。

## <a name="prerequisites"></a>先决条件

* 来自 [Azure 逻辑应用所支持的任何电子邮件提供程序](../connectors/apis-list.md)用于发送通知的电子邮件帐户，如 Office 365 Outlook、Outlook.com 或 Gmail。 本教程使用 Office 365 Outlook。

* [虚拟机](https://azure.microsoft.com/services/virtual-machines)。 如果你尚未这样做，则通过[创建 VM 教程](https://docs.microsoft.com/azure/virtual-machines/)创建虚拟机。 若要使虚拟机发布事件，你[无需执行任何其他操作](../event-grid/overview.md)。

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>创建通过事件网格监视事件的逻辑应用

首先，创建逻辑应用并添加事件网格触发器，以便监视虚拟机的资源组。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 在 Azure 菜单的左上角，选择“新建” > “Enterprise Integration” > “逻辑应用”。

   ![创建逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. 使用下表中指定的设置创建逻辑应用：

   ![提供逻辑应用的详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *{your-logic-app-name}* | 提供一个唯一的逻辑应用名称。 | 
   | **订阅** | *{your-Azure-subscription}* | 在本教程中，选择同一 Azure 订阅用于所有服务。 | 
   | **资源组** | *{your-Azure-resource-group}* | 在本教程中，选择同一 Azure 资源组用于所有服务。 | 
   | **位置** | *{your-Azure-region}* | 在本教程中，选择同一区域用于所有服务。 | 
   | | | 

4. 准备就绪后，请选择“固定到仪表板”，并选择“创建”。

   你现在已为逻辑应用程序创建 Azure 资源。 
   在 Azure 部署逻辑应用后，逻辑应用设计器会显示针对常用模式的模板，以便你可以更快地入门。

   > [!NOTE] 
   > 选择“固定到仪表板”时，逻辑应用会在逻辑应用设计器中自动打开。 否则，你可以手动查找和打开逻辑应用。

5. 接着，选择逻辑应用模板。 在“模板”下，选择“空白逻辑应用”，以便可以从头开始构建逻辑应用。

   ![选择逻辑应用模板](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   逻辑应用设计器现显示[连接器](../connectors/apis-list.md)和[触发器](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)，可用来启动逻辑应用，以及在触发器之后添加执行任务的操作。 触发器是创建逻辑应用实例并开始逻辑应用工作流的事件。 
   逻辑应用需要触发器作为第一项。

6. 在搜索框中，输入“事件网格”作为筛选器。 选择此触发器：“Azure 事件网格 - 在资源事件上”

   ![选择此触发器：“Azure 事件网格 - 在资源事件上”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. 出现提示时，请使用 Azure 凭据登录到 Azure 事件网格。

   ![使用 Azure 凭据登录](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > 如果使用个人 Microsoft 帐户登录，如 @outlook.com 或 @hotmail.com，事件网格触发器可能不会正确显示。 作为一种解决方法，选择[与服务主体连接](/azure-resource-manager/resource-group-create-service-principal-portal.md)，或作为与 Azure 订阅关联的 Azure Active Directory 成员进行身份验证，例如，user-name@emailoutlook.onmicrosoft.com。

8. 现在订阅发布者事件的逻辑应用。 提供你在下表中指定的事件订阅的详细信息：

   ![提供事件订阅的详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **订阅** | *{virtual-machine-Azure-subscription}* | 选择事件发布者的 Azure 订阅。 对于本教程，请选择用于虚拟机的 Azure 订阅。 | 
   | **资源类型** | Microsoft.Resources.resourceGroups | 选择事件发布者的资源类型。 对于本教程，请选择相应的指定值，以便逻辑应用仅监视资源组。 | 
   | 资源名称 | *{virtual-machine-resource-group-name}* | 选择发布者的资源名称。 对于本教程，请选择适用于你的虚拟机的资源组名称。 | 
   | 对于可选设置，请选择“显示高级选项”。 | *{see descriptions}* | * 前缀筛选器：对于本教程，请将此设置留空。 默认行为与所有值匹配。 但是，你可以指定一个前缀字符串作为筛选器，例如，特定资源的路径和参数。 <p>* 后缀筛选器：对于本教程，请将此设置留空。 默认行为与所有值匹配。 但是，当你仅需要特定文件类型，可以指定一个后缀字符串作为筛选器，例如，文件扩展名。<p>* 订阅名称：提供事件订阅的唯一名称。 |
   | | | 

   完成时，事件网格触发器可能如以下示例所示：
   
   ![示例事件网格触发器详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. 保存逻辑应用。 在设计器工具栏上，选择“保存”。 若要折叠和隐藏逻辑应用中操作的详细信息，请选择操作的标题栏。

   ![保存逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   当你使用事件网格触发器保存逻辑应用时，Azure 将自动为选定资源的逻辑应用创建事件订阅。 因此，当资源将事件发布到事件网格时，该事件网格会自动将事件推送到逻辑应用。 此事件触发逻辑应用，然后将创建并运行你在这些后续步骤中定义的工作流实例。

逻辑应用现已发布，并侦听事件网格中的事件，但在将操作添加到工作流之前它不会执行任何操作。 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>添加一个条件以检查虚拟机更改

若要仅在特定事件发生时运行逻辑应用工作流，请添加检查虚拟机“写入”操作的条件。 如果此条件为 true，你的逻辑应用会向你发送包含已更新虚拟机详细信息的电子邮件。

1. 在“逻辑应用设计器”的事件网格触发器下，选择“新步骤” > “添加条件”。

   ![将条件添加到逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   逻辑应用设计器将在工作流中添加一个空条件，包括要遵循的操作路径，具体要取决于条件为 true 还是 false。

   ![空条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. 在“条件”框中，选择“在高级模式下编辑”。
输入此表达式：

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   现在，条件应如下例所示：

   ![空条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   此表达式将检查事件 `body` 的 `data` 对象，其中 `operationName` 属性是 `Microsoft.Compute/virtualMachines/write` 操作。 
   详细了解[事件网格事件架构](../event-grid/event-schema.md)。

3. 若要提供条件说明，请选择条件形状上的“省略号”(“...”) 按钮，然后选择“重命名”。

   > [!NOTE] 
   > 本教程后面的示例还提供了逻辑应用工作流中的步骤说明。

4. 现在选择“在基本模式下编辑”，以便表达式自动解析，如下所示：

   ![逻辑应用条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. 保存逻辑应用。

## <a name="send-email-when-your-virtual-machine-changes"></a>虚拟机更改时发送电子邮件

现在添加[操作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)，以便在指定条件为 true 时收到电子邮件。

1. 在该条件的“如果为 true”框中，选择“添加操作”。

   ![在条件为 true 时添加操作](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. 在搜索框中，输入“电子邮件”作为筛选器。 根据你的电子邮件提供程序，找到并选择匹配的连接器。 然后选择连接器的“发送电子邮件”操作。 例如： 

   * 对于 Azure 工作或学校帐户，请选择 Office 365 Outlook 连接器。 
   * 对于个人 Microsoft 帐户，请选择 Outlook.com 连接器。 
   * 对于 Gmail 帐户，则选择 Gmail 连接器。 

   我们将继续使用 Office 365 Outlook 连接器。 
   如果你使用不同的提供程序，步骤保持不变，但你的 UI 显示可能会有所不同。 

   ![选择“发送电子邮件”操作](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. 如果你还没有与你的电子邮件提供程序建立连接，则在系统要求你进行身份验证时登录到电子邮件帐户。

4. 提供下表中指定的电子邮件的详细信息：

   ![空的电子邮件操作](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > 若要选择工作流中可用的字段，请单击“编辑”框，以便打开“动态内容”列表，或选择“添加动态内容”。 对于多个字段，请选择列表中每个部分的“查看更多”。 若要关闭“动态内容”列表，请选择“添加动态内容”。

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **To** | *{recipient-email-address}* |输入收件人的电子邮件地址。 为进行测试，可以使用自己的电子邮件地址。 | 
   | **主题** | 更新资源：主题| 输入电子邮件的主题内容。 对于本教程，请输入建议的文本并选择该事件的“主题”字段。 此处，电子邮件主题包含更新资源（虚拟机）的名称。 | 
   | **正文** | 资源组：主题 <p>事件类型：事件类型<p>事件 ID：ID<p>时间：事件时间 | 输入电子邮件的正文内容。 对于本课程，请输入建议的文本并选择事件的“主题”、“事件类型”和“ID”和“事件时间”字段，以便电子邮件包括资源组名称、事件类型、事件时间戳和用于更新的事件 ID。 <p>若要在内容中添加空行，请按 Shift + Enter。 | 
   | | | 

   > [!NOTE] 
   > 如果你选择表示数组的字段，设计器会围绕引用数组的操作自动添加“For each”循环。 这样一来，逻辑应用会对每个数组项执行该操作。

   现在，电子邮件操作可能如下例所示：

   ![选择要包含在电子邮件中的输出](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   而完成的逻辑应用可能如下例所示：

   ![完成的逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. 保存逻辑应用。 若要折叠和隐藏逻辑应用中每个操作的详细信息，请选择操作的标题栏。

   ![保存逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   逻辑应用现已发布，但在执行任何操作之前会等待对虚拟机的更改。 
   若要现在测试逻辑应用，请继续学习下一节。

## <a name="test-your-logic-app-workflow"></a>测试逻辑应用工作流

1. 若要检查逻辑应用是否将获取指定事件，请更新你的虚拟机。 

   例如，你可以在 Azure 门户中或[使用 Azure PowerShell 重设你的虚拟机大小](../virtual-machines/windows/resize-vm.md)。 

   几分钟后，你应会收到一封电子邮件。 例如：

   ![有关虚拟机更新的电子邮件](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. 若要查看逻辑应用的运行与触发历史记录，请在逻辑应用菜单中选择“概述”。 若要查看有关运行的更多详细信息，请选择运行所在的行。

   ![逻辑应用运行历史记录](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. 若要查看每个步骤的输入和输出，请展开要查看的步骤。 此信息可以帮助你诊断和调试逻辑应用中的问题。
 
   ![逻辑应用运行历史记录详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

祝贺你，你已经创建并运行逻辑应用，它将通过事件网格监视资源事件，并在这些事件发生时向你发送电子邮件。 此外，还学习了如何轻松创建工作流，以便将流程自动化，并将系统和云服务相集成。

## <a name="clean-up-resources"></a>清理资源

本教程使用的资源和执行的操作将会在你的 Azure 订阅上产生费用。 因此，完成本教程和测试后，请确保禁用或删除你不希望产生费用的任何资源。

你可以停止逻辑应用的运行和发送电子邮件，而不删除此应用。 在逻辑应用菜单上，选择“概述”。 在工具栏上，选择“禁用”。

![关闭逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>常见问题

问：我还可以使用事件网格和逻辑应用执行哪些其他虚拟机监控任务？ </br>
答：你可以监视其他配置更改，例如：

* 虚拟机获取基于角色的访问控制 (RBAC) 权限。
* 对网络接口 (NIC) 上的网络安全组 (NSG) 进行的更改。
* 添加或删除虚拟机磁盘。
* 公共 IP 地址被分配给虚拟机 NIC。

## <a name="next-steps"></a>后续步骤

* [事件网格概述](../event-grid/overview.md)
* [事件网格概念](../event-grid/concepts.md)
* [快速入门：使用事件网格创建和路由自定义事件](../event-grid/custom-event-quickstart.md)
* [事件网格事件架构](../event-grid/event-schema.md)
* [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)
* [使用预定义模板创建逻辑应用工作流](../logic-apps/logic-apps-use-logic-app-templates.md)

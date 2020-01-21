---
title: 监视虚拟机更改 - Azure 事件网格和逻辑应用
description: 通过使用 Azure 事件网格和逻辑应用检查虚拟机 (VM) 中的更改
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982573"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>教程：通过 Azure 事件网格和逻辑应用监视虚拟机更改

若要监视并响应在 Azure 资源或第三方资源中发生的特定事件，可以创建使用最少代码的[逻辑应用](../logic-apps/logic-apps-overview.md)，以便以工作流的方式实现任务的自动化操作和运行。 这些资源可以将事件发布到 [Azure 事件网格](../event-grid/overview.md)。 然后，事件网格会将这些事件推送给具有队列、webhook 或[事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)作为终结点的订阅者。 作为订阅者，逻辑应用可以在运行自动化工作流以执行任务之前等待这些来自事件网格的事件。

例如，下面是发布者可以将通过 Azure 事件网格服务发送给订阅者的某些事件：

* 创建、读取、更新或删除资源。 例如，你可以监视可能在 Azure 订阅中产生费用并影响你账单的更改。

* 从 Azure 订阅添加或删除某个人。

* 你的应用可执行特定的操作。

* 队列中显示新消息。

本教程将创建逻辑应用，用于监视对虚拟机的更改并就这些更改发送电子邮件。 当你为 Azure 资源创建具有事件订阅的逻辑应用时，事件会通过事件网格从该资源流向逻辑应用。 本教程将指导你完成构建此逻辑应用：

![概述 - 通过事件网格和逻辑应用监视虚拟机](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建通过事件网格监视事件的逻辑应用。
> * 添加一个专门检查虚拟机更改的条件。
> * 虚拟机更改时发送电子邮件。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 来自逻辑应用支持的电子邮件提供程序且用于发送通知的电子邮件帐户，例如 Office 365 Outlook、Outlook.com 或 Gmail。 至于其他提供商，请[查看此处的连接器列表](/connectors/)。

  本教程将使用 Office 365 Outlook 帐户。 如果使用其他电子邮件帐户，则常规步骤保持不变，但 UI 显示可能稍有不同。

* 一个在其自己的 Azure 资源组中独一无二的[虚拟机](https://azure.microsoft.com/services/virtual-machines)。 如你尚未这样做，请通过[创建 VM 教程](../virtual-machines/windows/quick-create-portal.md)创建虚拟机。 若要使虚拟机发布事件，你[无需执行任何其他操作](../event-grid/overview.md)。

## <a name="create-blank-logic-app"></a>创建空白逻辑应用

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单中，依次选择“创建资源” > “集成” > “逻辑应用”。   

   ![创建逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. 在“逻辑应用”下，提供有关逻辑应用资源的信息  。 完成操作后，选择“创建”  。

   ![提供逻辑应用的详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | properties | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*logic-app-name*> | 提供逻辑应用的唯一名称。 |
   | **订阅** | 是 | <*Azure-subscription-name*> | 在本教程中，选择同一 Azure 订阅用于所有服务。 |
   | **资源组** | 是 | <Azure-resource-group>  | 逻辑应用的 Azure 资源组名称，可以在本教程中选择用于所有服务。 |
   | **位置** | 是 | <*Azure-region*> | 在本教程中，选择同一区域用于所有服务。 |
   |||

1. 在 Azure 部署逻辑应用以后，逻辑应用设计器会显示一个包含简介视频和常用触发器的页面。 滚动浏览视频和触发器。

1.   在“模板”下选择“空白逻辑应用”。

   ![选择逻辑应用模板](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   逻辑应用设计器现在显示可用于启动逻辑应用的[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)  。 每个逻辑应用都必须从触发器开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，Azure 逻辑应用都会创建一个运行逻辑应用的工作流实例。

## <a name="add-an-event-grid-trigger"></a>添加事件网格触发器

现在，请添加事件网格触发器，用于监视虚拟机的资源组。

1. 在设计器上的搜索框中，输入 `event grid` 作为筛选器。 从触发器列表中选择“当资源事件发生时”触发器。 

   ![选择此触发器：“发生资源事件时”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. 出现提示时，请使用 Azure 帐户凭据登录到 Azure 事件网格。 在“租户”列表（其中显示与 Azure 订阅关联的 Azure Active Directory 租户）中，检查是否显示了正确的租户，例如： 

   ![使用 Azure 凭据登录](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > 如果使用个人 Microsoft 帐户登录，如 @outlook.com 或 @hotmail.com，事件网格触发器可能不会正确显示。 作为一种解决方法，选择[与服务主体连接](../active-directory/develop/howto-create-service-principal-portal.md)，或作为与 Azure 订阅关联的 Azure Active Directory 成员进行身份验证，例如，user-name  @emailoutlook.onmicrosoft.com。

1. 现在，通过逻辑应用订阅发布者的事件。 提供下表所述事件订阅的详细信息，例如：

   ![提供事件订阅的详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | properties | 必选 | 值 | 说明 |
   | -------- | -------- | ----- | ----------- |
   | **订阅** | 是 | <*event-publisher-Azure-subscription-name*> | 选择与事件发布者关联的 Azure 订阅名称。  对于本教程，请选择用于虚拟机的 Azure 订阅名称。 |
   | **资源类型** | 是 | <*event-publisher-Azure-resource-type*> | 选择事件发布者的 Azure 资源类型。 有关 Azure 资源类型的详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。 对于本教程，请选择 `Microsoft.Resources.ResourceGroups` 值以监视 Azure 资源组。 |
   | 资源名称  |  是 | <*event-publisher-Azure-resource-name*> | 选择事件发布者的 Azure 资源名称。 此列表依据所选择的资源类型而异。 对于本教程，请选择虚拟机所在的 Azure 资源组的名称。 |
   | **事件类型项** |  否 | <*event-types*> | 选择一个或多个特定事件类型以筛选并发送到事件网格。 例如，可选择添加这些事件类型以检测何时更改或删除了资源： <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>有关详细信息，请参阅以下主题： <p><p>- [资源组的 Azure 事件网格事件架构](../event-grid/event-schema-resource-groups.md) <br>- [了解事件筛选](../event-grid/event-filtering.md) <br>- [筛选事件网格的事件](../event-grid/how-to-filter-events.md) |
   | 若要添加可选属性，请选择“添加新参数”，  然后选择所需属性。 | 否 | {参阅说明} | * **前缀筛选器**：对于本教程，请将此属性留空。 默认行为与所有值匹配。 但是，你可以指定一个前缀字符串作为筛选器，例如，特定资源的路径和参数。 <p>* **后缀筛选器**：对于本教程，请将此属性留空。 默认行为与所有值匹配。 但是，当你仅需要特定文件类型，可以指定一个后缀字符串作为筛选器，例如，文件扩展名。 <p>* **订阅名称**：在本教程中，可提供事件订阅的唯一名称。 |
   |||

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”  。 若要折叠和隐藏逻辑应用中操作的详细信息，请选择操作的标题栏。

   ![保存逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   当你使用事件网格触发器保存逻辑应用时，Azure 将自动为选定资源的逻辑应用创建事件订阅。 因此，当资源将事件发布到事件网格时，该事件网格会自动将事件推送到逻辑应用。 此事件触发逻辑应用，然后将创建并运行你在这些后续步骤中定义的工作流实例。

逻辑应用现已发布，并侦听事件网格中的事件，但在将操作添加到工作流之前它不会执行任何操作。

## <a name="add-a-condition"></a>添加条件

如果希望逻辑应用仅在特定事件或操作发生时运行，请添加一个检查是否存在 `Microsoft.Compute/virtualMachines/write` 操作的条件。 如果此条件为 true，你的逻辑应用会向你发送包含已更新虚拟机详细信息的电子邮件。

1. 在“逻辑应用设计器”的事件网格触发器下，选择“新步骤”  。

   ![选择“新步骤”](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. 在“选择操作”  下的搜索框中，输入 `condition` 作为筛选器。 从操作列表中选择“条件”操作。 

   ![添加条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   逻辑应用设计器将在工作流中添加一个空条件，包括要遵循的操作路径，具体要取决于条件为 true 还是 false。

   ![出现一个空条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. 将条件标题重命名为 `If a virtual machine in your resource group has changed`。 在条件的标题栏中，选择省略号 (...) 按钮，然后选择“重命名”   。

   ![将条件重命名](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. 创建一个条件，用于检查事件 `body` 的 `data` 对象中的 `operationName` 属性是否等于 `Microsoft.Compute/virtualMachines/write` 操作。 详细了解[事件网格事件架构](../event-grid/event-schema.md)。

   1. 在“和”下面的第一行中，单击左侧框的内部。  在显示的“动态内容”中，选择“表达式”  。

      ![选择“表达式”，打开表达式编辑器](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. 在表达式编辑器中输入此表达式（用于从触发器返回操作名称），然后选择“确定”  ：

      `triggerBody()?['data']['operationName']`

      例如：

      ![输入用于提取操作名称的表达式](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. 在中间框中，保留运算符“等于”。 

   1. 在右框中输入此值（要监视的具体操作）：

      `Microsoft.Compute/virtualMachines/write`

   现在，已完成的条件应如下例所示：

   ![已完成的条件，用于比较操作](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   如果从设计视图切换到代码视图并返回到设计视图，则在条件中指定的表达式将解析为 data.operationName 标记： 

   ![条件中已解析的令牌](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. 保存逻辑应用。

## <a name="send-email-notifications"></a>发送电子邮件通知

现在添加[操作  ](../logic-apps/logic-apps-overview.md#logic-app-concepts)，以便在指定条件为 true 时接收电子邮件。

1. 在条件的 **If true** 框中，选择“添加操作”。 

   ![在条件为 true 时添加操作](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. 在“选择操作”  下的搜索框中，输入 `send an email` 作为筛选器。 根据你的电子邮件提供程序，找到并选择匹配的连接器。 然后选择连接器的“发送电子邮件”操作。 例如：

   * 对于 Azure 工作或学校帐户，请选择 Office 365 Outlook 连接器。

   * 对于个人 Microsoft 帐户，请选择 Outlook.com 连接器。

   * 对于 Gmail 帐户，则选择 Gmail 连接器。

   本教程将继续使用 Office 365 Outlook 连接器。 如果使用其他提供程序，步骤将保持不变，但 UI 显示可能会略有不同。

   ![选择“发送电子邮件”操作](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. 如果你还没有与你的电子邮件提供程序建立连接，则在系统要求你进行身份验证时登录到电子邮件帐户。

1. 将“发送电子邮件”操作重命名为此标题：`Send email when virtual machine updated`

1. 提供下表中指定的电子邮件的信息：

   ![提供关于电子邮件操作的信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > 若要选择工作流中的前述步骤的输出，请单击编辑框以显示动态内容列表，或选择“添加动态内容”  。 对于多个结果，请选择列表中每个部分的“查看更多”  。 若要关闭“动态内容”列表，请再次选择“添加动态内容”  。

   | properties | 必选 | 值 | 说明 |
   | -------- | -------- | ----- | ----------- |
   | **收件人** | 是 | <*接收方\@域*> | 输入收件人的电子邮件地址。 为进行测试，可以使用自己的电子邮件地址。 |
   | **主题** | 是 | `Resource updated:` **主题** | 输入电子邮件的主题内容。 对于本教程，请输入指定的文本并选择该事件的“主题”字段  。 此处，电子邮件主题包含更新资源（虚拟机）的名称。 |
   | **正文** | 是 | `Resource:` **主题** <p>`Event type:` **事件类型**<p>`Event ID:` **ID**<p>`Time:` **事件时间** | 输入电子邮件的正文内容。 对于本教程，请输入指定的文本并选择事件的“主题”、“活动类型”、“ID”和“事件时间”字段，以便电子邮件包含触发事件的资源、事件类型、事件时间戳和更新的事件 ID     。 对于本教程，资源是在触发器中选择的 Azure 资源组。 <p>若要在内容中添加空行，请按 Shift + Enter。 |
   ||||

   > [!NOTE]
   > 如果你选择表示数组的字段，设计器会围绕引用数组的操作自动添加“For each”  循环。 这样一来，逻辑应用会对每个数组项执行该操作。

   现在，电子邮件操作可能如下例所示：

   ![选择要包含在电子邮件中的输出](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   而完成的逻辑应用可能如下例所示：

   ![完成的逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. 保存逻辑应用。 若要折叠和隐藏逻辑应用中每个操作的详细信息，请选择操作的标题栏。

   逻辑应用现已发布，但在执行任何操作之前会等待对虚拟机的更改。 若要现在测试逻辑应用，请继续学习下一节。

## <a name="test-your-logic-app-workflow"></a>测试逻辑应用工作流

1. 若要检查逻辑应用是否将获取指定事件，请更新你的虚拟机。

   例如，你可以在 Azure 门户中或[使用 Azure PowerShell 重设你的虚拟机大小](../virtual-machines/windows/resize-vm.md)。

   几分钟后，你应会收到一封电子邮件。 例如：

   ![有关虚拟机更新的电子邮件](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. 若要查看逻辑应用的运行与触发历史记录，请在逻辑应用菜单中选择“概览”  。 若要查看有关运行的更多详细信息，请选择运行所在的行。

   ![逻辑应用运行历史记录](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. 若要查看每个步骤的输入和输出，请展开要查看的步骤。 此信息可以帮助你诊断和调试逻辑应用中的问题。

   ![逻辑应用运行历史记录详细信息](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

祝贺你，你已经创建并运行逻辑应用，它将通过事件网格监视资源事件，并在这些事件发生时向你发送电子邮件。 此外，还学习了如何轻松创建工作流，以便将流程自动化，并将系统和云服务相集成。

可使用事件网格和逻辑应用监视其他配置更改，例如：

* 虚拟机获取基于角色的访问控制 (RBAC) 权限。
* 对网络接口 (NIC) 上的网络安全组 (NSG) 进行的更改。
* 添加或删除虚拟机磁盘。
* 公共 IP 地址被分配给虚拟机 NIC。

## <a name="clean-up-resources"></a>清理资源

本教程使用的资源和执行的操作将会在你的 Azure 订阅上产生费用。 因此，完成本教程和测试后，请确保禁用或删除你不希望产生费用的任何资源。

* 若要在不删除所做工作的情况下停止运行逻辑应用，请禁用应用。 在逻辑应用菜单中，选择“概述”  。 在工具栏中选择“禁用”。 

  ![关闭逻辑应用](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 如果看不到逻辑应用菜单，请尝试返回到 Azure 仪表板，然后重新打开逻辑应用。

* 若要永久删除逻辑应用，请在逻辑应用菜单上选择“概览”  。 在工具栏中选择“删除”。  确认要删除逻辑应用，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

* [使用事件网格创建和路由自定义事件](../event-grid/custom-event-quickstart.md)

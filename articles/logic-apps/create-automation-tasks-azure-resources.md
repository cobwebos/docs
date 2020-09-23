---
title: 创建自动化任务以管理和监视 Azure 资源
description: 设置自动化任务，以帮助你通过创建在 Azure 逻辑应用上运行的工作流来管理 Azure 资源和监视成本。
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 93c796fd16dde8c238265d16a96b9cfa4a254ea9
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994191"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a> (预览中创建自动化任务来管理 Azure 资源和监视成本) 

> [!IMPORTANT]
> 此功能以公共预览版提供，不提供服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

为了帮助你更轻松地管理 [Azure 资源](../azure-resource-manager/management/overview.md#terminology) ，你可以使用自动化任务模板为特定资源或资源组创建自动化管理任务，这些任务模板根据资源类型在可用性上有所不同。 例如，对于 [Azure 存储帐户](../storage/common/storage-account-overview.md)，你可以设置一个自动化任务，该任务会向你发送该存储帐户的每月费用。 对于 [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)，你可以创建一个自动化任务，该任务按预定义的计划启用或关闭虚拟机。

在幕后，自动化任务实际上是在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 服务上运行的工作流，并使用相同的 [定价费率](https://azure.microsoft.com/pricing/details/logic-apps/) 和 [定价模型](../logic-apps/logic-apps-pricing.md)进行计费。 创建任务后，可以通过在逻辑应用设计器中打开该任务来查看和编辑基础工作流。 任务至少完成一次运行后，可以查看每个运行的状态、历史记录、输入和输出。

下面是此预览版中当前可用的任务模板：

| 资源类型 | 自动化任务模板 |
|---------------|---------------------------|
| Azure 资源组 | **删除资源时** |
| 所有 Azure 资源 | **每月发送资源成本** |
| Azure 虚拟机 | 此外： <p>- **关闭虚拟机** <br>- **启动虚拟机** |
| Azure 存储帐户 | 此外： <p>- **删除旧 blob** |
|||

本文介绍如何完成以下任务：

* 为特定的 Azure 资源[创建自动化任务](#create-automation-task)。

* [查看任务的历史记录](#review-task-history)，其中包括运行状态、输入、输出和其他历史记录信息。

* [编辑任务](#edit-task) ，以便可以更新任务，或在逻辑应用设计器中自定义任务的基础工作流。

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>自动化任务如何不同于 Azure 自动化？

目前，只能在资源级别创建自动化任务，查看任务的运行历史记录，并编辑由 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 服务提供支持的任务的基础逻辑应用工作流。

[Azure 自动化](../automation/automation-intro.md) 是一项基于云的自动化和配置服务，支持跨 azure 和非 Azure 环境进行一致的管理。 该服务通过使用[runbook](../automation/automation-runbook-execution.md)、配置管理[和更改跟踪和清单](../automation/change-tracking.md)、更新管理、共享功能和异类功能，[来协调处理流程](../automation/automation-intro.md#process-automation)。 在部署、操作和解除工作负荷与资源期间，自动化可以提供全面的控制。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要管理的 Azure 资源。 本文使用 Azure 存储帐户作为示例。

* Office 365 帐户（如果你想要遵循示例），该帐户将使用 Office 365 Outlook 向你发送电子邮件。

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>创建自动化任务

1. 在 [Azure 门户](https://portal.azure.com)中，找到要管理的资源。

1. 在 "资源" 菜单上，滚动到 "**自动化**" 部分，然后选择 "**任务**"

   ![显示 "自动化" 部分所选 "任务" 菜单项的 "Azure 门户" 和 "存储帐户" 资源菜单的屏幕截图。](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. 在 " **任务** " 窗格中，选择 " **添加** "，以便可以选择任务模板。

   ![显示存储帐户 "任务" 窗格的屏幕截图，其中的工具栏已选中 "添加"](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. 在 " **添加任务** " 窗格中的 " **选择模板**" 下，选择要创建的任务的模板，然后选择 " **下一步：身份验证**"。

   此示例通过选择 " **每月发送资源** 任务模板" 任务模板继续。

   ![显示选择 "每月发送资源成本" 和 "下一步：身份验证" 的屏幕截图](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. 在 " **身份验证**" 下的 " **连接** " 部分中，为每个连接选择 " **创建** "，以便可以为该连接提供身份验证凭据。 每个任务中的连接类型根据任务的不同而异。

   ![显示 Azure 资源管理器连接的选定 "创建" 选项的屏幕截图](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. 出现提示时，请用 Azure 帐户凭据登录。

   ![显示选择 "登录" 的屏幕截图](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   每个成功通过身份验证的连接看起来类似于以下示例：

   ![显示已成功创建连接的屏幕截图](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. 验证所有所需的连接后，选择 " **下一步：配置**"。

1. 在 " **配置**" 下，提供任务的名称以及任务所需的任何其他信息。 完成操作后，选择“创建”  。

   > [!NOTE]
   > 创建后无法更改任务名称，因此请考虑在 [编辑基础工作流](#edit-task-workflow)时仍适用的名称。 对基础工作流所做的更改将仅应用于所创建的任务，而不是任务模板。
   >
   > 例如，如果将任务命名为 `Send monthly cost` ，但随后编辑基础工作流以每周运行，则不能将任务的名称更改为 `Send weekly cost` 。

   发送电子邮件通知的任务需要电子邮件地址。

   ![显示选定任务所需的信息的屏幕截图](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   你创建的任务（该任务自动处于活动状态）随即出现在 " **自动化任务** " 列表中。

   ![显示自动化任务列表的屏幕截图](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > 如果该任务不会立即出现，请尝试刷新任务列表或等待片刻，然后刷新。 在工具栏上，选择 " **刷新**"。

   选定任务运行后，会收到如下所示的电子邮件：

   ![显示任务发送的电子邮件通知的屏幕截图](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>查看任务历史记录

若要查看任务及其状态、输入、输出和其他信息的运行历史记录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，查找包含要查看的任务历史记录的资源。

1. 在资源菜单上的 " **设置**" 下，选择 " **自动化任务**"。

1. 在 "任务" 列表中，找到要查看的任务。 在该任务的 " **运行** " 列中，选择 " **查看**"。

   ![显示任务和选定的 "视图" 选项的屏幕截图](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   " **运行历史记录** " 窗格显示任务的所有运行及其状态、开始时间、标识符和运行持续时间。

   ![显示任务运行、其状态和其他信息的屏幕截图](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   下面是运行的可能状态：

   | 状态 | 说明 |
   |--------|-------------|
   | 已取消 | 任务在运行时被取消。 |
   | 失败 | 任务至少具有一个失败的操作，但不存在任何用于处理失败的后续操作。 |
   | **正在运行** | 任务当前正在运行。 |
   | 成功 | 所有操作成功。 如果操作失败，则任务仍可以成功完成，但后续操作存在来处理失败。 |
   | **正在等待** | 该运行尚未开始，已暂停，因为该任务的更早实例仍在运行。 |
   |||

   有关详细信息，请参阅 [查看运行历史记录](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. 若要查看运行中每个步骤的状态和其他信息，请选择该运行。

   此时将打开 " **逻辑应用运行** " 窗格，并显示运行的基础工作流。

   * 工作流始终以 [*触发器*](../connectors/apis-list.md#triggers-actions)开头。 对于此任务，工作流从 [**定期** 触发器](../connectors/connectors-native-recurrence.md)开始。

   * 每个步骤都显示其状态和运行持续时间。 运行时间为0秒的步骤所用的时间不到1秒。

   ![显示运行状况、状态和运行持续时间的每个步骤的屏幕截图](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. 若要查看每个步骤的输入和输出，请选择要扩展的步骤。

   此示例显示了重复触发器的输入，该触发器没有输出，因为触发器仅指定工作流的运行时间，不提供后续操作要处理的输出。

   ![显示展开触发器和输入的屏幕截图](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   与此相反，" **发送电子邮件** " 操作具有工作流中之前操作的输入和输出。

   ![显示扩展操作、输入和输出的屏幕截图](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

若要了解如何构建自己的自动化工作流，以便可以将应用、数据、服务和系统与 Azure 资源的自动化任务上下文分开集成，请参阅 [快速入门：使用 Azure 逻辑应用创建第一个集成工作流-Azure 门户](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="edit-task"></a>

## <a name="edit-the-task"></a>编辑任务

若要更改任务，你可以使用以下选项：

* [编辑任务 "内联"](#edit-task-inline) ，以便可以更改任务的属性，例如连接信息或配置信息，例如，电子邮件地址。

* 在逻辑应用设计器中[编辑任务的基础工作流](#edit-task-workflow)。

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>直接编辑任务

1. 在 [Azure 门户](https://portal.azure.com)中，找到包含要更新的任务的资源。

1. 在资源菜单上的 " **自动化**" 下，选择 " **任务**"。

1. 在 "任务" 列表中，找到要更新的任务。 打开任务的 "省略号 (**...** ") 菜单，然后选择 **"以行编辑"**。

   ![屏幕截图，显示打开的省略号菜单和选定的选项 "编辑内联"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   默认情况下，" **身份验证** " 选项卡将出现并显示现有连接。

1. 若要添加新的身份验证凭据或为连接选择不同的现有身份验证凭据，请打开连接的 "省略号 (**...** ") 菜单，然后选择 " **添加新连接** " 或 "如果可用，则使用不同的身份验证凭据"。

   ![显示 "身份验证" 选项卡、现有连接和所选省略号菜单的屏幕截图](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. 若要更新其他任务属性，请选择 " **下一步：配置**"。

   对于本示例中的任务，唯一可用于编辑的属性是电子邮件地址。

   ![显示 "配置" 选项卡的屏幕截图](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. 完成后，选择“保存”。

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>编辑任务的基础工作流

更改自动化任务的基础工作流时，所做的更改只会影响你创建的任务实例，而不会影响创建任务的模板。 进行更改并保存更改后，为原始任务提供的名称可能无法准确准确地描述该任务，因此可能需要使用其他名称重新创建任务。

> [!TIP]
> 作为最佳做法，请克隆基础工作流，以便可以改为编辑复制的版本。 这样一来，你就可以在副本上进行更改并对其进行测试，而原始自动化任务会继续运行，而不会影响损坏或破坏现有功能。 完成更改后，如果新版本已成功运行，则可以禁用或删除原始自动化任务，并为自动化任务使用克隆的版本。 以下步骤介绍了如何克隆工作流。

1. 在 [Azure 门户](https://portal.azure.com)中，找到包含要更新的任务的资源。

1. 在资源菜单上的 " **自动化**" 下，选择 " **任务**"。

1. 在 "任务" 列表中，找到要更新的任务。 打开任务的 "省略号 (**...** ") 菜单，然后选择 " **在逻辑应用中打开**"。

   ![屏幕截图，显示打开的省略号菜单和选择的选项 "在逻辑应用中打开"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   任务的基础工作流在 Azure 逻辑应用服务中打开，并显示 " **概述** " 窗格，可在其中查看可用于任务的相同运行历史记录。

   ![在选择了概述窗格的 Azure 逻辑应用视图中显示任务的屏幕截图](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. 若要在逻辑应用设计器中打开基础工作流，请在逻辑应用的菜单上，选择 " **逻辑应用设计器**"。

   ![显示 "逻辑应用设计器" 菜单选项和设计器图面以及基础工作流的屏幕截图](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   你现在可以编辑工作流的触发器和操作的属性，以及编辑用于定义工作流本身的触发器和操作。 但是，作为最佳实践，请按照步骤克隆工作流，以便在原始工作流继续运行和运行的同时对副本进行更改。

1. 若要克隆工作流并改为编辑复制的版本，请执行以下步骤：

   1. 在 "逻辑应用工作流" 菜单上，选择 " **概述**"。

   1. 在 "概述" 窗格的工具栏上，选择 " **克隆**"。

   1. 在 "逻辑应用创建" 窗格的 " **名称**" 下，为复制的逻辑应用工作流输入新名称。

      除了 **逻辑应用状态**之外，其他属性不可编辑。 
      
   1. 在 " **逻辑应用状态**" 下，选择 " **禁用** "，以便在进行更改时不会运行克隆的工作流。 准备好测试更改时，可以启用工作流。

   1. Azure 完成预配克隆的工作流后，请在逻辑应用设计器中查找并打开该工作流。

1. 若要查看触发器或操作的属性，请展开该触发器或操作。

   例如，可以将定期触发器更改为每周运行一次，而不是每月运行一次。

   ![屏幕截图，显示展开的定期触发器，其中显示了 Frequency 列表，显示可用频率选项](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   有关定期触发器的详细信息，请参阅 [创建、计划和运行定期触发器的重复执行任务和工作流](../connectors/connectors-native-recurrence.md)。 有关可使用的其他触发器和操作的详细信息，请参阅 [Azure 逻辑应用的连接器](../connectors/apis-list.md)。

1. 若要保存更改，请在设计器工具栏上选择 " **保存**"。

   ![显示设计器工具栏和所选 "保存" 命令的屏幕截图](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. 若要测试并运行更新的工作流，请在设计器工具栏上选择 " **运行**"。

   运行完成后，设计器将显示工作流的运行详细信息。

   ![在设计器中显示工作流的运行详细信息的屏幕截图](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. 若要禁用工作流以使任务不会继续运行，请参阅 [在 Azure 门户中管理逻辑应用](../logic-apps/manage-logic-apps-with-azure-portal.md)。

## <a name="provide-feedback"></a>提供反馈

我们想要倾听你的意见！ 若要报告 bug、提供反馈或询问有关此预览功能的问题，请 [联系 Azure 逻辑应用团队](mailto:logicapps@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 门户中管理逻辑应用](../logic-apps/manage-logic-apps-with-azure-portal.md)

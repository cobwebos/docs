---
title: 使用 Azure 构建基于计划的自动化工作流
description: 教程 - 通过使用 Azure 逻辑应用创建跨云服务集成的、基于计划的、定期执行的自动化工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 3bf4ad12bab3e71675ff35203bf69526b3b8614f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574511"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>教程：使用 Azure 逻辑应用创建基于计划的、定期执行的自动化工作流

本教程介绍了如何构建一个示例[逻辑应用](../logic-apps/logic-apps-overview.md)，用于自动执行一个按定期计划运行的工作流。 具体而言，此示例逻辑应用将在每个工作日的早晨运行，检查两个地点之间的行程时间，包括交通情况。 如果该时间超过特定的限制，逻辑应用会向你发送一封电子邮件，其中包括到达目的地所需的行程时间和额外时间。 该工作流包括各种步骤，这些步骤从基于计划的触发器开始，后跟必应地图操作、数据操作、控制流操作和电子邮件通知操作。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空白逻辑应用。
> * 添加一个重复触发器用于指定逻辑应用的计划。
> * 添加一个必应地图操作用于获取路线的行程时间。
> * 添加一个操作用于创建变量、将行程时间从秒转换为分钟，并将结果存储在变量中。
> * 添加一个条件，将行程时间与指定的限制进行比较。
> * 添加一个操作，以便在行程时间超过限制的情况下发送电子邮件。

完成后，逻辑应用看起来大致与以下工作流类似：

![屏幕截图显示了示例逻辑应用工作流概览。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 逻辑应用支持的电子邮件提供商（例如 Office 365 Outlook、Outlook.com 或 Gmail）提供的电子邮件帐户。 至于其他提供商，请[查看此处的连接器列表](/connectors/)。 本快速入门通过工作或学校帐户使用 Office 365 Outlook。 如果你使用其他电子邮件帐户，则常规步骤保持不变，但 UI 可能稍有不同。

  > [!IMPORTANT]
  > 如果要使用 Gmail 连接器，则只有 G-Suite 商业帐户可以在逻辑应用中不受限制地使用此连接器。 如果有 Gmail 用户帐户，则只能将此连接器与 Google 批准的特定服务一起使用，也可以[创建用于通过 Gmail 连接器进行身份验证的 Google 客户端应用](/connectors/gmail/#authentication-and-bring-your-own-application)。 有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

* 若要获取路线的行程时间，需要必应地图 API 的访问密钥。 若要获取此密钥，请执行[如何获取必应地图密钥](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)中的步骤。

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主页上，选择“创建资源”。

1. 在“Azure 市场”菜单上，选择“集成” > “逻辑应用”。

   ![屏幕截图显示了“Azure 市场”菜单，其中选定了“集成”和“逻辑应用”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. 在“逻辑应用”窗格上，提供此处所述的有关要创建的逻辑应用的信息。

   ![屏幕截图显示了逻辑应用创建窗格和要为新逻辑应用提供的信息。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | properties | 值 | 说明 |
   |----------|-------|-------------|
   | **订阅** | <*Azure-subscription-name*> | Azure 订阅名称。 本示例使用 `Pay-As-You-Go`。 |
   | **资源组** | LA-TravelTime-RG | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/management/overview.md)的名称。 此示例创建一个名为 `LA-TravelTime-RG` 的新资源组。 |
   | **Name** | LA-TravelTime | 逻辑应用的名称，只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）和句点 (`.`)。 本示例使用 `LA-TravelTime`。 |
   | **位置** | 美国西部 | 用于存储逻辑应用信息的区域。 本示例使用 `West US`。 |
   | **Log Analytics** | 关闭 | 对于诊断日志记录，请保留“关闭”设置。 |
   ||||

1. 完成操作后，选择“查看 + 创建”。 在 Azure 验证你的逻辑应用的相关信息后，选择“创建”。

1. 在 Azure 部署应用后，选择“转到资源”。

   Azure 会打开逻辑应用模板选择窗格，其中显示了简介视频、常用触发器和逻辑应用模板模式。

1. 向下滚动，越过视频和常用触发器部分，找到“模板”部分，然后选择“空白逻辑应用”。

   ![屏幕截图显示了逻辑应用模板选择窗格，其中选择了“空白逻辑应用”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

接下来，添加根据指定计划运行工作流的 Recurrence [触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)。 每个逻辑应用都必须从触发器开始，在发生特定事件或新数据符合特定条件的情况下触发。 有关详细信息，请参阅[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-the-recurrence-trigger"></a>添加重复触发器

1. 在逻辑应用设计器搜索框中输入 `recurrence`，然后选择名为“Recurrence”的触发器。

   ![屏幕截图显示了逻辑应用设计器搜索框，其中包含“recurrence”搜索词，而在“触发器”列表中，“Recurrence”触发器显示为选中状态。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. 在“重复”形状中选择**省略号** ( **...** ) 按钮，然后选择“重命名”。  重命名触发器并提供以下说明：`Check travel time every weekday morning`

   ![屏幕截图显示省略号按钮处于选定状态，“设置”列表处于打开状态，“重命名”命令处于选定状态。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. 在触发器中更改这些属性，如此处所述和所示。

   ![屏幕截图显示了对触发器的间隔和频率的更改。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | properties | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **时间间隔** | 是 | 1 | 在两次检查之间需等待的时间间隔数 |
   | **频率** | 是 | Week | 用于定期触发的时间单位 |
   |||||

1. 在“间隔”和“频率”下，打开“添加新参数”列表，然后选择要添加到触发器的这些属性。  

   * **在这些日期**
   * **在这些小时**
   * **在这些分钟**

   ![屏幕截图显示了打开的“添加新参数”列表和以下选定的属性：“在这些天”、“在这些小时”和“在这些分钟”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 现在，按此处所示和所述设置其他属性的值。

   ![屏幕截图显示了设置为下表中所述值的其他属性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | properties | 值 | 说明 |
   |----------|-------|-------------|
   | **在这些日期** | 星期一、星期二、星期三、星期四、星期五 | 只有将“频率”设置为“周”时，此设置才可用。 |
   | **在这些小时** | 7,8,9 | 只有将“频率”设置为“周”或“天”时，此设置才可用。 对于此定期，请选择一天中的某些小时。 此示例在 `7`、`8` 和 `9` 点运行。 |
   | **在这些分钟** | 0,15,30,45 | 只有将“频率”设置为“周”或“天”时，此设置才可用。 对于此定期，请选择一天中的某些分钟。 此示例在零点启动，每隔 15 分钟运行一次。 |
   ||||

   此触发器在每个工作日每隔 15 分钟触发一次，开始时间为早晨 7:00，结束时间为早晨 9:45。 “预览”框显示定期触发计划。 有关详细信息，请参阅[计划任务和工作流](../connectors/connectors-native-recurrence.md)以及[工作流操作和触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。

1. 若要立即隐藏触发器的详细信息，请在形状的标题栏内单击以折叠形状。

   ![屏幕截图显示了折叠的触发器形状。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。

你的逻辑应用现已位于 Azure 门户中，但除了根据指定计划进行触发之外，它不会执行任何操作。 因此，请添加一项在触发器触发时进行响应的操作。

## <a name="get-the-travel-time-for-a-route"></a>获取路线的行程时间

有了触发器以后，即可添加[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)，用于获取两个地点之间的行程时间。 逻辑应用提供适用于必应地图 API 的连接器，可以方便地获取该信息。 在开始此任务之前，请确保有必应地图 API 密钥，如本教程的先决条件部分所述。

1. 在逻辑应用设计器的 Recurrence 触发器下，选择“新建步骤”。

1. 在“选择操作”下，选择“标准”。  在搜索框中输入 `bing maps`，然后选择名为“获取路线”的操作。

   ![屏幕截图显示了按“必应地图”操作筛选的“选择操作”列表，“获取路线”操作处于选定状态。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. 如果没有必应地图连接，系统会提示创建一个连接。 提供如下所示和所述的连接详细信息，然后选择“创建”。

   ![屏幕截图显示了必应地图连接框，其中具有指定的连接名称和必应地图 API 密钥。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | properties | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **连接名称** | 是 | BingMapsConnection | 提供连接的名称。 本示例使用 `BingMapsConnection`。 |
   | **API 密钥** | 是 | <Bing-Maps-API-key> | 输入你之前收到的必应地图 API 密钥。 如果没有必应地图密钥，请了解[如何获取密钥](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)。 |
   |||||

1. 重命名此操作并提供以下说明：`Get route and travel time with traffic`。

1. 在操作中，打开“添加新参数”列表，然后选择这些属性。

   * **优化**
   * **距离单位**
   * **旅行模式**

   ![屏幕截图显示了“获取路线...”操作，其中选定了“优化”、“距离单位”和“旅行模式”属性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 现在输入属性的值，如此处所示和所述。

   ![屏幕截图显示了“获取路线”操作的其他属性值。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | properties | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **路标 1** | 是 | <*start-location*> | 你的路线的起点。 此示例指定了一个示例起始地址。 |
   | **路标 2** | 是 | <*end-location*> | 你的路线的终点。 此示例指定了一个示例终点地址。 |
   | **优化** | 否 | timeWithTraffic | 一个参数，用于优化路线，例如距离、当前交通状况下的行程时间，等等。 选择参数值 **timeWithTraffic**。 |
   | **距离单位** | 否 | <*your-preference*> | 路线的距离单位。 此示例使用“英里”作为单位。 |
   | **旅行模式** | 否 | 驾车 | 路线的旅行模式。 选择“驾车”模式。 |
   |||||

   有关这些参数和值的详细信息，请参阅[计算路线](/bingmaps/rest-services/routes/calculate-a-route)。

1. 在设计器工具栏上选择“保存”。

接下来创建一个变量，以便将当前的行程时间转换为分钟而非秒，然后将其存储。 这样即可避免重复转换，可以在后面的步骤中更方便地使用该值。 

## <a name="create-a-variable-to-store-travel-time"></a>创建用于存储行程时间的变量

有时候，可能需要对工作流中的数据运行操作，再在以后的操作中使用相关结果。 若要保存这些结果，方便以后重复使用或引用，可以创建变量来存储这些处理后的结果。 只能在逻辑应用的顶层创建变量。

默认情况下，“获取路线”操作通过“旅行期间交通”属性返回特定交通状况下的当前行程时间（以秒为单位）。  将该值改为转换为分钟并进行存储以后，该值在以后就会更易于重复使用，不需再次转换。

1. 在设计器上的“获取路线”操作下，选择“新建步骤”。 

1. 在“选择操作”下，选择“内置”。  在搜索框中输入 `variables`，然后选择名为“初始化变量”的操作。

   ![屏幕截图显示了处于选定状态的“初始化变量”操作。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. 重命名此操作并提供以下说明：`Create variable to store travel time`

1. 为你的变量提供此信息，如此表和表下方的步骤所示：

   | properties | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | travelTime | 变量的名称。 本示例使用 `travelTime`。 |
   | 类型 | 是 | Integer | 变量的数据类型 |
   | **值** | 否 | 一个表达式，可将当前的行程时间从秒转换为分钟（参见此表下面的步骤）。 | 变量的初始值 |
   |||||

   1. 若要创建“值”属性的表达式，请单击该框，使动态内容列表显示。 必要时可扩大浏览器，直至动态列表显示。 在动态内容列表中，选择“表达式”，这将显示表达式编辑器。

      ![屏幕截图显示了“初始化变量”操作（光标位于“值”属性内），此操作将打开动态内容列表。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      动态内容列表显示以前的操作的输出，你可以选择这些输出作为工作流中后续操作的输入。 动态内容列表包括一个表达式编辑器，你可以使用该编辑器来选择在表达式中执行操作的函数。 此表达式编辑器仅显示在动态内容列表中。

   1. 在表达式编辑器中，输入此表达式：`div(,60)`

      ![屏幕截图显示了表达式编辑器，其中输入了“div(,60)”表达式。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 在表达式内，将光标置于左括号 ( **(** ) 与逗号 ( **,** ) 之间，并选择“动态内容”。

      ![屏幕截图显示了在“div(,60)”表达式中放置光标的位置，其中选择了“动态内容”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 在动态内容列表中，选择属性值“旅行期间交通”。

      ![屏幕截图显示了处于选定状态的“旅行期间交通”属性值。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 在表达式中解析属性值后，选择“确定”。

      ![屏幕截图显示了处于选定状态的“确定”按钮。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      “值”属性现在如下所示：

      ![屏幕截图显示了带解析后的表达式的“值”属性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. 保存逻辑应用。

接下来添加一个条件，检查当前的行程时间是否长于特定的限制。

## <a name="compare-the-travel-time-with-limit"></a>将行程时间与限制进行比较

1. 在“创建变量以存储旅行时间”操作下，选择“新建步骤”。

1. 在“选择操作”下，选择“内置”。  在搜索框中输入 `condition`。 从操作列表中，选择名为“条件”的操作。

   ![屏幕截图显示了处于选定状态的“条件”操作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. 重命名条件并提供以下说明：`If travel time exceeds limit`

1. 生成一个条件，用于检查 **travelTime** 属性值是否超过指定的限制，如下所示：

   1. 在条件中，单击条件左侧的“选择值”框。

   1. 从显示的动态内容列表中的“变量”下，选择名为 **travelTime** 的属性。

      ![屏幕截图显示了条件左侧的“选择值”框，其中打开了动态内容列表，并选择了“travelTime”属性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 在中间的比较框中，选择名为“大于”的运算符。

   1. 在条件右侧的“选择值”框中输入以下限制：`15`

      完成后，条件如以下示例所示：

      ![屏幕截图显示了用于将旅行时间与指定限制进行比较的已完成条件。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. 保存逻辑应用。

接下来，添加在行程时间超出限制时需运行的操作。

## <a name="send-email-when-limit-exceeded"></a>在超出限制时发送电子邮件

现在，添加一个操作，用于在旅行时间超出限制时向你发送电子邮件。 该电子邮件包括当前的行程时间以及行完指定的路线所需的额外时间。

1. 在条件的 **True** 分支中，选择“添加操作”。

1. 在“选择操作”下，选择“标准”。  在搜索框中输入 `send email`。 此列表会返回许多结果，因此请首先选择所需的电子邮件连接器，以便对列表进行筛选。

   例如，如果你有一个 Outlook 电子邮件帐户，请选择适用于你的帐户类型的连接器：

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。

   本示例接下来选择 Office 365 Outlook。

   ![屏幕截图显示了“选择操作列表”，其中选择了“标准”类别和“Office 365 Outlook”连接器。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. 显示连接器的操作后，选择发送电子邮件的操作，例如：

   ![屏幕截图显示了处于选定状态的“发送电子邮件”操作。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. 如果还没有连接，请在系统提示时登录并验证对你的电子邮件帐户的访问权限。

   Azure 逻辑应用创建到电子邮件帐户的连接。

1. 重命名操作并提供以下说明：`Send email with travel time`

1. 对于“收件人”属性，输入收件人的电子邮件地址。 为进行测试，可以使用你的电子邮件地址。

1. 对于“主题”属性，通过执行以下步骤指定电子邮件的主题，并包括 **travelTime** 变量：

   1. 输入带尾随空格的文本 `Current travel time (minutes):`。 将光标置于“主题”框中，使动态内容列表保持打开状态。

   1. 从动态内容列表的“变量”标题中选择“查看更多”，以便显示名为 **travelTime** 的变量。

      ![屏幕截图显示了包含“变量”部分的动态内容列表，“查看更多”处于选中状态。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > 动态内容列表不会自动显示 **travelTime** 变量，因为“主题”属性需要字符串值，而 **travelTime** 是整数值。

      ![屏幕截图显示了动态内容列表，并选定了“travelTime”变量。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. 对于“正文”属性，请按照以下步骤指定电子邮件正文的内容：

   1. 输入带尾随空格的文本 `Add extra travel time (minutes):`。 将光标置于“正文”框中，使动态内容列表保持打开状态。

   1. 在动态内容列表中，选择“表达式”，这将显示表达式编辑器。

      ![屏幕截图显示了动态内容列表，其中选定了“表达式”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 在表达式编辑器中输入 `sub(,15)`，以便计算超出限制的分钟数： 

      ![屏幕截图显示了表达式编辑器，其中输入了“sub(,15)”表达式。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 在表达式内，将光标置于左括号 ( **(** ) 与逗号 ( **,** ) 之间，并选择“动态内容”。

      ![屏幕截图显示了在“sub(,15)”表达式中放置光标的位置，其中选择了“动态内容”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. 在“变量”下选择“travelTime”。 

      ![屏幕截图显示了动态内容列表，并选定了“travelTime”变量。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 在表达式中解析属性后，选择“确定”。

      ![屏幕截图显示了动态内容列表，并选定了“确定”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      “正文”属性现在如下所示：

      ![屏幕截图显示了动态内容列表，以及在电子邮件操作的“正文”属性中解析的表达式。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. 保存逻辑应用。

接下来，测试并运行你的逻辑应用，该应用现在看起来类似于以下示例：

![屏幕截图显示了已完成的示例逻辑应用工作流](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>运行逻辑应用

若要手动启动逻辑应用，请在设计器工具栏中选择“运行”。

* 如果当前的行程时间始终没有超出限制，则逻辑应用不会执行任何其他操作，只是等待下一个时间间隔，然后重新进行检查。 

* 如果当前的行程时间超出限制，则会收到一封电子邮件，其中包含当前的行程时间以及超出限制的分钟数。 下面是逻辑应用发送的一封示例电子邮件：

  ![屏幕截图显示了示例电子邮件，它报告了当前旅行时间和超过指定限制的额外旅行时间。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > 如果没有收到任何电子邮件，请检查电子邮件的垃圾邮件文件夹。 垃圾电子邮件筛选器可能会将这些类型的邮件重定向。 否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现已创建并运行基于计划的重复逻辑应用。 

若要创建其他使用“重复”触发器的逻辑应用，请检查以下模板，这些模板在创建逻辑应用之后即可使用：

* 获取发送给你的每日提醒。
* 删除较旧的 Azure Blob。
* 将消息添加到 Azure 存储队列中。

## <a name="clean-up-resources"></a>清理资源

在禁用或删除你的逻辑应用之前，该逻辑应用会一直运行。 不再需要示例逻辑应用时，请删除包含该逻辑应用和相关资源的资源组。

1. 在 Azure 门户的搜索框中，输入你创建的资源组的名称。 从结果中的“资源组”下，选择该资源组。

   此示例创建一个名为 `LA-TravelTime-RG` 的资源组。 

   ![屏幕截图显示了 Azure 搜索框，其中输入了“la-travel-time-rg”并选择了“LA-TravelTime-RG”。****](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > 如果 Azure 主页在“最近的资源”下显示了该资源组，则可以从主页中选择该组。

1. 在资源组菜单上，检查是否已选中“概览”。 在“概览”窗格的工具栏上，选择“删除资源组”。 

   ![屏幕截图显示了资源组的“概览”窗格，并在窗格的工具栏上选择了“删除资源组”。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 在确认窗格中，输入资源组名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建一个逻辑应用，以便根据指定的计划（在工作日早晨）检查交通状况，并在行程时间超出指定限制时采取行动（发送电子邮件）。 现在，需了解如何通过集成 Azure 服务、Microsoft 服务和其他软件即服务 (SaaS) 应用来生成一个逻辑应用，以便发送供审核的邮件列表请求。

> [!div class="nextstepaction"]
> [管理邮件列表请求](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)

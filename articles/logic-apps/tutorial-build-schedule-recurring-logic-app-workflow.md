---
title: 生成基于计划的自动化工作流
description: 教程 - 使用 Azure 逻辑应用创建基于计划的重复自动化工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456630"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>教程：使用 Azure 逻辑应用创建自动化的基于计划的重复工作流

本教程介绍如何生成[逻辑应用](../logic-apps/logic-apps-overview.md)，并自动化一个按计划运行的重复工作流。 具体而言，此示例逻辑应用将在每个工作日的早晨运行，检查两个地点之间的行程时间，包括交通情况。 如果该时间超过特定的限制，逻辑应用会发送一封电子邮件，其中包括到达目的地所需的行程时间和额外时间。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空白逻辑应用。
> * 添加一个重复触发器用于指定逻辑应用的计划。
> * 添加一个必应地图操作用于获取路线的行程时间。
> * 添加一个操作用于创建变量、将行程时间从秒转换为分钟，并将结果存储在变量中。
> * 添加一个条件，将行程时间与指定的限制进行比较。
> * 添加一个操作，以便在行程时间超过限制的情况下发送电子邮件。

完成后，逻辑应用看起来大致与以下工作流类似：

![高级逻辑应用工作流概述](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果你没有 Azure 订阅，请在开始之前[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 逻辑应用支持的电子邮件提供商（例如 Office 365 Outlook、Outlook.com 或 Gmail）提供的电子邮件帐户。 至于其他提供商，请[查看此处的连接器列表](https://docs.microsoft.com/connectors/)。 本快速入门使用 Office 365 Outlook 帐户。 如果你使用其他电子邮件帐户，则常规步骤保持不变，但 UI 可能稍有不同。

* 若要获取路线的行程时间，需要必应地图 API 的访问密钥。 若要获取此密钥，请执行[如何获取必应地图密钥](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)中的步骤。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 在 Azure 主菜单中，依次选择“创建资源” > “集成” > “逻辑应用”。   

   ![创建逻辑应用资源](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. 在“创建逻辑应用”下，提供有关逻辑应用的信息，如下所示。  完成操作后，选择“创建”  。

   ![提供有关逻辑应用的信息](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | properties | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | LA-TravelTime | 逻辑应用的名称，只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）和句点 (`.`)。 此示例使用“LA-TravelTime”。 |
   | **订阅** | <*your-Azure-subscription-name*> | Azure 订阅名称 |
   | **资源组** | LA-TravelTime-RG | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/management/overview.md)的名称。 此示例使用“LA-TravelTime-RG”。 |
   | **位置** | 美国西部 | 用于存储逻辑应用信息的区域。 此示例使用“美国西部”。 |
   | **Log Analytics** | 关闭 | 对于诊断日志记录，请保留“关闭”设置。  |
   ||||

1. 在 Azure 部署你的应用后，在 Azure 工具栏上，选择“通知”   > “转到资源”  ，查看你部署的逻辑应用。

   ![转到新的逻辑应用资源](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   或者，可以通过在搜索框中键入名称来查找和选择逻辑应用。

   逻辑应用设计器打开并显示一个包含简介视频以及常用触发器和逻辑应用模式的页面。   在“模板”下选择“空白逻辑应用”。

   ![选择空白逻辑应用模板](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

接下来，请添加根据指定计划激发的重复[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)。 每个逻辑应用都必须从触发器开始，在发生特定事件或新数据符合特定条件的情况下触发。 有关详细信息，请参阅[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-the-recurrence-trigger"></a>添加重复触发器

1. 在逻辑应用设计器上的搜索框中，输入“重复”作为筛选器。 在“触发器”列表中选择“重复”触发器。  

   ![添加“重复”触发器](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. 在“重复”形状中选择**省略号** ( **...** ) 按钮，然后选择“重命名”。   重命名触发器并提供以下说明：`Check travel time every weekday morning`

   ![重命名重复触发器说明](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. 在触发器中更改这些属性。

   ![更改重复触发器的间隔和频率](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | properties | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | 间隔  | 是 | 1 | 在两次检查之间需等待的时间间隔数 |
   | **频率** | 是 | Week | 用于定期触发的时间单位 |
   |||||

1. 在“间隔”和“频率”下，打开“添加新参数”列表，然后选择要添加到触发器的这些属性。   

   * **在这些日期**
   * **在这些小时**
   * **在这些分钟**

   ![添加重复触发器的属性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 现在，按此处所示和所述设置其他属性的值。

   ![提供计划和定期触发详细信息](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | properties | 值 | 说明 |
   |----------|-------|-------------|
   | **在这些日期** | 星期一、星期二、星期三、星期四、星期五 | 仅当“频率”设置为“周”时可用  |
   | **在这些小时** | 7,8,9 | 仅当“频率”设置为“周”或“天”时可用  。 选择一天中的某些小时，用于运行此定期触发。 此示例在 7、8、9 点运行。 |
   | **在这些分钟** | 0,15,30,45 | 仅当“频率”设置为“周”或“天”时可用  。 选择一天中的某些分钟，用于运行此定期触发。 此示例从零点开始，每隔 15 分钟运行一次。 |
   ||||

   此触发器在每个工作日每隔 15 分钟触发一次，开始时间为早晨 7:00，结束时间为早晨 9:45。 “预览”框显示定期触发计划。  有关详细信息，请参阅[计划任务和工作流](../connectors/connectors-native-recurrence.md)以及[工作流操作和触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。

1. 若要立即隐藏触发器的详细信息，请单击形状的标题栏。

   ![折叠形状即可隐藏详细信息](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”  。

逻辑应用现已生成，但除了定期触发，不能执行任何操作。 因此，请添加一项在触发器触发时进行响应的操作。

## <a name="get-the-travel-time-for-a-route"></a>获取路线的行程时间

有了触发器以后，即可添加[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)，用于获取两个地点之间的行程时间。 逻辑应用提供适用于必应地图 API 的连接器，可以方便地获取该信息。 在开始此任务之前，请确保有必应地图 API 密钥，如本教程的先决条件部分所述。

1. 在逻辑应用设计器中的触发器下，选择“新建步骤”。 

1. 在“选择操作”下选择“标准”。   在搜索框中输入“必应地图”作为筛选器，然后选择“获取路线”操作。 

   ![选择“获取路线”操作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. 如果没有必应地图连接，系统会提示创建一个连接。 提供以下连接详细信息，然后选择“创建”  。

   ![创建与必应地图 API 的连接](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | properties | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **连接名称** | 是 | BingMapsConnection | 提供连接的名称。 此示例使用“BingMapsConnection”。 |
   | **API 密钥** | 是 | <*your-Bing-Maps-key*> | 输入以前接收的必应地图密钥。 如果没有必应地图密钥，请了解[如何获取密钥](https://msdn.microsoft.com/library/ff428642.aspx)。 |
   |||||

1. 重命名操作并提供以下说明：`Get route and travel time with traffic`

1. 在该操作中打开“添加新参数”列表，然后选择要添加到该操作的这些属性。 

   * **优化**
   * **距离单位**
   * **旅行模式**

   ![将属性添加到“获取路线”操作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 现在，按此处所示和所述设置操作属性的值。

   ![提供“获取路线”操作的详细信息](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | properties | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **路标 1** | 是 | <*start-location*> | 路线起点 |
   | **路标 2** | 是 | <*end-location*> | 路由终点 |
   | **优化** | 否 | timeWithTraffic | 一个参数，用于优化路线，例如距离、当前交通状况下的行程时间，等等。 选择“timeWithTraffic”参数。 |
   | **距离单位** | 否 | <*your-preference*> | 路线的距离单位。 此示例使用“英里”作为单位。 |
   | **旅行模式** | 否 | 驾车 | 路线的旅行模式。 选择“驾车”模式。 |
   ||||

   有关这些参数的详细信息，请参阅 [Calculate a route](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route)（计算路线）。

1. 保存逻辑应用。

接下来创建一个变量，以便将当前的行程时间转换为分钟而非秒，然后将其存储。 这样即可避免重复转换，可以在后面的步骤中更方便地使用该值。 

## <a name="create-a-variable-to-store-travel-time"></a>创建用于存储行程时间的变量

有时候，可能需要对工作流中的数据运行操作，再在以后的操作中使用相关结果。 若要保存这些结果，方便以后重复使用或引用，可以创建变量来存储这些经处理的结果。 只能在逻辑应用的顶层创建变量。

默认情况下，以前的“获取路线”操作通过“旅行期间交通”属性返回特定交通状况下的当前行程时间（以秒为单位）。   将该值改为转换为分钟并进行存储以后，该值在以后就会更易于重复使用，不需再次转换。

1. 在“获取路线”操作下，选择“新建步骤”。  

1. 在“选择操作”下，选择“内置”。   在搜索框中输入“变量”，然后选择“初始化变量”操作。 

   ![选择“初始化变量”操作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. 重命名此操作并提供以下说明：`Create variable to store travel time`

1. 提供变量的详细信息，如下所述：

   | properties | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | travelTime | 变量的名称。 此示例使用“travelTime”。 |
   | 类型  | 是 | Integer | 变量的数据类型 |
   | **值** | 否| 一个表达式，可将当前的行程时间从秒转换为分钟（参见此表下面的步骤）。 | 变量的初始值 |
   ||||

   1. 若要创建“值”属性的表达式，请单击该框，使动态内容列表显示。  必要时可扩大浏览器，直至列表显示。 在动态内容列表中，选择“表达式”。 

      ![提供“初始化变量”操作的信息](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      单击某些编辑框时，动态内容列表会显示。 此列表显示以前的操作中任何可以在工作流中用作输入的属性。 动态内容列表有一个表达式编辑器，可以在其中选择用于运行操作的函数。 此表达式编辑器仅显示在动态内容列表中。

   1. 在表达式编辑器中，输入此表达式：`div(,60)`

      ![输入此表达式：“div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 将光标置于表达式中左括号 ( **(** ) 和逗号 ( **,** ) 之间。 
   选择“动态内容”。 

      ![位置光标，选择“动态内容”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 在动态内容列表中，选择“旅行期间交通”  。

      ![选择“旅行期间交通”属性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 在表达式中解析属性值后，选择“确定”。 

      ![若要完成构建表达式，请选择“确定”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      “值”属性现在如下所示： 

      ![“值”属性与已解析表达式一起出现](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. 保存逻辑应用。

接下来添加一个条件，检查当前的行程时间是否长于特定的限制。

## <a name="compare-the-travel-time-with-limit"></a>将行程时间与限制进行比较

1. 在上一个操作下，选择“新建步骤”。 

1. 在“选择操作”下，选择“内置”。   在搜索框中，输入“条件”作为筛选器。 从操作列表中选择“条件”操作。 

   ![选择“条件”操作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. 重命名条件并提供以下说明：`If travel time exceeds limit`

1. 生成一个条件，用于检查 **travelTime** 属性值是否超过指定的限制，如下所示：

   1. 在条件中，单击条件左侧的“选择值”框。 

   1. 从显示的动态内容列表中的“变量”  下，选择 **travelTime** 属性。

      ![构建条件的左侧](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 在中间的比较框中，选择“大于”  运算符。

   1. 在条件右侧的“选择值”框中输入以下限制：`15` 

      完成后，条件如以下示例所示：

      ![检查行程时间的完成条件](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. 保存逻辑应用。

接下来，添加在行程时间超出限制时需运行的操作。

## <a name="send-email-when-limit-exceeded"></a>在超出限制时发送电子邮件

现在，请添加一项操作，该操作在行程时间超出限制时向你发送电子邮件。 该电子邮件包括当前的行程时间以及行完指定的路线所需的额外时间。

1. 在条件的 **If true** 分支中，选择“添加操作”。 

1. 在“选择操作”下选择“标准”。   在搜索框中，输入“发送电子邮件”。 此列表将返回许多结果，因此请先选择所需的电子邮件连接器，例如：

   ![选择所需的电子邮件连接器](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。 
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。 

1. 显示连接器的操作后，选择要使用的“发送电子邮件”操作，例如：

   ![选择“发送电子邮件”操作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. 如果还没有连接，系统会要求你登录到电子邮件帐户。

   逻辑应用创建到电子邮件帐户的连接。

1. 重命名操作并提供以下说明：`Send email with travel time`

1. 在“收件人”  框中，输入收件人的电子邮件地址。 出于测试目的，请使用你的电子邮件地址。

1. 在“主题”框中指定电子邮件的主题，并包括 **travelTime** 变量。 

   1. 输入带尾随空格的文本 `Current travel time (minutes):`。 

   1. 在动态内容列表中的“变量”下，选择“查看更多”。  

      ![查找“travelTime”变量](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. “travelTime”显示在“变量”下面后，选择“travelTime”。   

      ![输入主题文本以及可返回行程时间的表达式](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. 在“正文”框中，指定电子邮件正文的内容。 

   1. 输入带尾随空格的文本 `Add extra travel time (minutes):`。

   1. 在动态内容列表中，选择“表达式”。 

      ![生成电子邮件正文的表达式](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 在表达式编辑器中输入以下表达式，以便计算超出限制的分钟数：```sub(,15)```

      ![输入用于计算额外行程时间（单位：分钟）的表达式](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 将光标置于表达式中左括号 ( **(** ) 和逗号 ( **,** ) 之间。 选择“动态内容”。 

      ![继续生成用于计算额外行程时间（单位：分钟）的表达式](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. 在“变量”下选择“travelTime”。  

      ![选择要在表达式中使用的“travelTime”属性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 在表达式中解析属性后，选择“确定”。 

      ![在“正文”属性解析后，选择“确定”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      “正文”属性现在如下所示： 

      ![解析了表达式中的“正文”属性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. 保存逻辑应用。

接下来测试逻辑应用，该应用现在看起来类似于以下示例：

![完成的示例逻辑应用工作流](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>运行逻辑应用

若要手动启动逻辑应用，请在设计器工具栏中选择“运行”  。

* 如果当前的行程时间始终没有超出限制，则逻辑应用不会执行任何其他操作，只是等待下一个时间间隔，然后重新进行检查。 

* 如果当前的行程时间超出限制，则会收到一封电子邮件，其中包含当前的行程时间以及超出限制的分钟数。 下面是逻辑应用发送的一封示例电子邮件：

![显示行程时间的已发送电子邮件示例](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

如果没有收到任何电子邮件，请检查电子邮件的垃圾邮件文件夹。 垃圾电子邮件筛选器可能会将这些类型的邮件重定向。 否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现已创建并运行基于计划的重复逻辑应用。 

若要创建其他使用“重复”触发器的逻辑应用，请检查以下模板，这些模板在创建逻辑应用之后即可使用： 

* 获取发送给你的每日提醒。
* 删除较旧的 Azure Blob。
* 将消息添加到 Azure 存储队列中。

## <a name="clean-up-resources"></a>清理资源

不再需要示例逻辑应用时，请删除包含该逻辑应用和相关资源的资源组。 

1. 在 Azure 主菜单中转到“资源组”，然后选择逻辑应用的资源组。 

1. 在资源组菜单中，选择“概述” > “删除资源组”。   

   ![“概览”>“删除资源组”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 输入资源组名称作为确认，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建一个逻辑应用，以便根据指定的计划（在工作日早晨）检查交通状况，并在行程时间超出指定限制时采取行动（发送电子邮件）。 现在，需了解如何通过集成 Azure 服务、Microsoft 服务和其他 SaaS 应用来生成一个逻辑应用，以便发送供审核的邮件列表请求。

> [!div class="nextstepaction"]
> [管理邮件列表请求](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)

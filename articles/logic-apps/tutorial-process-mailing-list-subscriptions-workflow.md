---
title: 生成基于审批的自动化工作流 - Azure 逻辑应用
description: 教程 - 使用 Azure 逻辑应用来创建基于审批的自动化工作流，以便处理邮件列表订阅
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: f720e22542533d17fc7ab581f8ba8d9c03a89570
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025583"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>教程：使用 Azure 逻辑应用创建自动化的基于审批的工作流

本教程介绍如何生成[逻辑应用](../logic-apps/logic-apps-overview.md)，以便自动完成基于审批的工作流。 具体说来，此逻辑应用处理通过 [MailChimp](https://mailchimp.com/) 服务托管的邮件列表的订阅请求。 此逻辑应用会监视电子邮件帐户中是否存在这些请求，发送这些请求进行审批，并向邮件列表添加批准的成员。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建空白逻辑应用。
> * 添加一个触发器，用于监视电子邮件中的订阅请求。
> * 添加一项操作，用于发送批准或拒绝这些请求的电子邮件。
> * 添加一项操作，用于检查审批响应。
> * 添加一项操作，用于向邮件列表添加批准的成员。
> * 添加一个条件，用于检查这些成员是否已成功加入列表。
> * 添加一项操作，其发送的电子邮件用于确认这些成员是否已成功加入列表。

完成后，逻辑应用看起来大致与以下工作流类似：

![完成的高级逻辑应用概述](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果你没有 Azure 订阅，请在开始之前[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个 MailChimp 帐户，其中包含名为“test-members-ML”的列表，供逻辑应用在其中为批准的成员添加电子邮件地址。 如果没有帐户，请[注册免费帐户](https://login.mailchimp.com/signup/)，然后了解[如何创建 MailChimp 列表](https://us17.admin.mailchimp.com/lists/#)。

* Office 365 Outlook 或 Outlook.com 中的电子邮件帐户，支持审批工作流。 本文使用 Office 365 Outlook。 如果使用其他电子邮件帐户，则常规步骤保持不变，但 UI 显示可能稍有不同。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 在 Azure 主菜单中，依次选择“创建资源” > “集成” > “逻辑应用”。   

   ![创建新的逻辑应用资源](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. 在“创建逻辑应用”下，提供有关逻辑应用的信息，如下所示。  完成操作后，选择“创建”  。

   ![提供有关逻辑应用的信息](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | LA-MailingList | 逻辑应用的名称，只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）和句点 (`.`)。 此示例使用“LA-MailingList”。 |
   | **订阅** | <*your-Azure-subscription-name*> | Azure 订阅名称 |
   | **资源组** | LA-MailingList-RG | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称。 此示例使用“LA-MailingList-RG”。 |
   | **位置** | 美国西部 | 用于存储逻辑应用信息的区域。 此示例使用“美国西部”。 |
   | **Log Analytics** | 关闭 | 对于诊断日志记录，请保留“关闭”设置。  |
   ||||

1. 在 Azure 部署你的应用后，在 Azure 工具栏上，选择“通知”   > “转到资源”  ，查看你部署的逻辑应用。

   ![转到新的逻辑应用资源](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   或者，可以通过在搜索框中键入名称来查找和选择逻辑应用。

   逻辑应用设计器打开并显示一个包含简介视频以及常用触发器和逻辑应用模式的页面。   在“模板”下选择“空白逻辑应用”。

   ![选择空白逻辑应用模板](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

接下来，请添加一个[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)，侦听包含订阅请求的传入电子邮件。 每个逻辑应用都必须从触发器开始，在发生特定事件或新数据符合特定条件的情况下触发。 有关详细信息，请参阅[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-trigger-to-monitor-emails"></a>添加用于监视电子邮件的触发器

1. 在逻辑应用设计器上的搜索框中，输入 `when email arrives` 作为筛选器。 从“触发器”列表中，为电子邮件提供商选择“收到新电子邮件时”触发器。  

   此示例使用 Office 365 Outlook 触发器：

   ![为电子邮件提供商选择“收到新电子邮件时”触发器](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。

1. 如果系统要求，请使用凭据登录电子邮件帐户，这样逻辑应用就可以创建到该电子邮件帐户的连接了。

1. 在触发器中，提供查看所有新电子邮件的条件。

   1. 指定用于检查电子邮件的文件夹、时间间隔和频率。

      ![指定用于检查邮件的文件夹、时间间隔和频率](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | 属性 | 值 | 说明 |
      |----------|-------|-------------|
      | **文件夹** | `Inbox` | 要监视的电子邮件文件夹 |
      | 间隔  | `1` | 在两次检查之间需等待的时间间隔数 |
      | **频率** | `Hour` | 用于定期触发的时间单位 |
      ||||

   1. 现在，为触发器添加另一属性，这样就可以在电子邮件主题行中进行筛选。 打开“添加新参数”列表，选择“主题筛选器”属性。  

      ![将“主题筛选器”属性添加到触发器](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      有关此触发器的属性的详细信息，请参阅 [Office 365 Outlook 连接器参考](https://docs.microsoft.com/connectors/office365/)或 [Outlook.com 连接器参考](https://docs.microsoft.com/connectors/outlook/)。

   1. 当属性显示在触发器中以后，请输入此文本：`subscribe-test-members-ML`

      ![为“主题筛选器”属性输入文本](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. 若要立即隐藏触发器的详细信息，请单击触发器的标题栏。

   ![折叠形状即可隐藏详细信息](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”  。

逻辑应用现已生成，但除了检查传入电子邮件，不能执行任何操作。 因此，请添加一项在触发器触发时进行响应的操作。

## <a name="send-approval-email"></a>发送批准电子邮件

有了触发器以后，即可添加一项[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)，以便发送批准或拒绝请求的电子邮件。

1. 在触发器下，选择“新建步骤”。  

1. 在“选择操作”  下的搜索框中，输入 `approval` 作为筛选器。 从操作列表中，为电子邮件提供商选择“发送审批电子邮件”操作。  

   此示例使用 Office 365 Outlook 操作：

   ![选择“发送审批电子邮件”操作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 根据以下说明提供有关此操作的信息： 

   ![“发送审批电子邮件”操作的属性](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **收件人** | <*your-email-address*> | 审批者的电子邮件地址。 可以使用自己的地址进行测试。 此示例使用虚构的“sophia.owen@fabrikam.com”电子邮件地址。 |
   | **主题** | `Approve member request for test-members-ML` | 一个描述性的电子邮件主题 |
   | **用户选项** | `Approve, Reject` | 审批者可以选择的响应选项。 默认情况下，审批者可以选择“批准”或“拒绝”作为其响应。 |
   ||||

   现在，请忽略在单击特定编辑框时会显示的动态内容列表。 可以通过此列表选择以前的操作提供的可用输出，这些输出可以在工作流中用作输入。

   有关此操作的属性的详细信息，请参阅 [Office 365 Outlook 连接器参考](https://docs.microsoft.com/connectors/office365/)或 [Outlook.com 连接器参考](https://docs.microsoft.com/connectors/outlook/)。
 
1. 保存逻辑应用。

接下来，请添加一个条件，用于检查审批者选定的响应。

## <a name="check-approval-response"></a>检查审批响应

1. 在“发送审批电子邮件”操作下，选择“新建步骤”。  

1. 在“选择操作”下，选择“内置”。   在搜索框中，输入 `condition` 作为筛选器。 从操作列表中选择“条件”操作。 

   ![查找并选择“条件”操作](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. 重命名此条件，并提供更好的说明。

   1. 在条件的标题栏中，选择**省略号** ( **...** ) 按钮 >“重命名”。 

      ![重命名条件的说明](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. 重命名条件并提供以下说明：`If request approved`

1. 生成一个条件，用于检查审批者是否选择了“批准”。 

   1. 在条件中，单击条件左侧的“选择值”框。 

   1. 从显示的动态内容列表中的“发送审批电子邮件”下选择 **SelectedOption** 属性。 

      ![从动态内容列表中，选择“SelectedOption”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 在中间的比较框中，选择“等于”  运算符。

   1. 在条件右侧的“选择值”框中输入此文本：`Approve` 

      完成后，条件如以下示例所示：

      ![已批准示例的完成条件](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. 保存逻辑应用。

接下来，请指定一项操作，供逻辑应用在审批者批准请求的情况下执行。 

## <a name="add-member-to-mailchimp-list"></a>将成员添加到 MailChimp 列表

现在请添加一项操作，用于将批准的成员添加到邮件列表。

1. 在条件的 **If true** 分支中，选择“添加操作”。 

1. 在“选择操作”  下，输入 `mailchimp` 作为筛选器，然后选择“将成员添加到列表”操作。 

   ![选择“将成员添加到列表”操作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. 如果系统提示你访问 MailChimp 帐户，请使用 MailChimp 凭据登录。

1. 提供有关此操作的信息，如下所示：

   ![为“将成员添加到列表”提供信息](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | 属性 | 必选 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **列表 ID** | 是 | `test-members-ML` | MailChimp 邮件列表的名称。 此示例使用“test-members-ML”。 |
   | **Status** | 是 | `subscribed` | 选择新成员的订阅状态。 本示例使用“subscribed”。 <p>有关详细信息，请参阅 [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)（使用 MailChimp API 管理订户）。 |
   | **电子邮件地址** | 是 | <*new-member-email-address*> | 在动态内容列表的“收到新电子邮件时”下选择“发件人”，以便传入新成员的电子邮件地址。   |
   ||||

   有关此操作的属性的详细信息，请参阅 [MailChimp 连接器参考](https://docs.microsoft.com/connectors/mailchimp/)。

1. 保存逻辑应用。

接下来，请添加一个条件，以便检查新成员是否已成功加入邮件列表。 这样一来，逻辑应用即可通知你该操作是成功还是失败。

## <a name="check-for-success-or-failure"></a>检查操作是成功还是失败

1. 在 **If true** 分支的“将成员添加到列表”操作下，  选择“添加操作”  。

1. 在“选择操作”下，选择“内置”。   在搜索框中，输入 `condition` 作为筛选器。 在操作列表中选择“条件”。 

1. 重命名条件并提供以下说明：`If add member succeeded`

1. 生成一个条件，用于检查批准的成员在加入邮件列表时是成功还是失败：

   1. 在条件中，单击条件左侧的“选择值”框。  在动态内容列表的“将成员添加到列表”下，选择“状态”属性。  

      例如，你的条件如以下示例所示：

      ![在“将成员添加到列表”下，选择“状态”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 在中间的比较框中，选择“等于”  运算符。

   1. 在条件右侧的“选择值”框中输入此文本：`subscribed` 

      完成后，条件如以下示例所示：

      ![已订阅示例的完成条件](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

接下来，请设置电子邮件，以便在批准的成员成功加入或无法加入邮件列表时发送。

## <a name="send-email-if-member-added"></a>在添加了成员的情况下发送电子邮件

1. 在“如果添加成员成功”条件的 **If true** 分支中，选择“添加操作”。  

   ![在“If true”分支中，选择“添加操作”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. 在“选择操作”  下的搜索框中，输入 `outlook send email` 作为筛选器，然后选择“发送电子邮件”操作。 

   ![添加“发送电子邮件”操作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. 重命名操作并提供以下说明：`Send email on success`

1. 提供此操作的信息，如下所示：

   ![提供成功电子邮件的信息](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | 属性 | 必选 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **收件人** | 是 | <*your-email-address*> | 一个电子邮件地址，可向其发送成功电子邮件。 为进行测试，可以使用自己的电子邮件地址。 |
   | **主题** | 是 | <*subject-for-success-email*> | 成功电子邮件的主题。 对于本教程，请输入以下文本： <p>`Success! Member added to "test-members-ML": ` <p>在动态内容列表的“将成员添加到列表”下，选择“电子邮件地址”属性。   |
   | **正文** | 是 | <*body-for-success-email*> | 成功电子邮件的正文内容。 对于本教程，请输入以下文本： <p>`New member has joined "test-members-ML":` <p>在动态内容列表中，选择“电子邮件地址”属性。  <p>在下一行中，输入此文本：`Member opt-in status: ` <p> 在动态内容列表的“将成员添加到列表”下，选择“状态”属性。   |
   |||||

1. 保存逻辑应用。

## <a name="send-email-if-member-not-added"></a>在添加不了成员的情况下发送电子邮件

1. 在“如果添加成员成功”条件的 **If false** 分支中，选择“添加操作”。  

   ![在“If false”分支中，选择“添加操作”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. 在“选择操作”  下的搜索框中，输入 `outlook send email` 作为筛选器，然后选择“发送电子邮件”操作。 

   ![为“发送电子邮件”添加操作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. 重命名操作并提供以下说明：`Send email on failure`

1. 提供有关此操作的信息，如下所示：

   ![提供失败电子邮件的信息](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | 属性 | 必选 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **收件人** | 是 | <*your-email-address*> | 一个电子邮件地址，可向其发送失败电子邮件。 为进行测试，可以使用自己的电子邮件地址。 |
   | **主题** | 是 | <*subject-for-failure-email*> | 失败电子邮件的主题。 对于本教程，请输入以下文本： <p>`Failed, member not added to "test-members-ML": ` <p>在动态内容列表的“将成员添加到列表”下，选择“电子邮件地址”属性。   |
   | **正文** | 是 | <*body-for-failure-email*> | 失败电子邮件的正文内容。 对于本教程，请输入以下文本： <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. 保存逻辑应用。 

接下来测试逻辑应用，该应用现在看起来类似于以下示例：

![完成的逻辑应用工作流示例](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>运行逻辑应用

1. 向自己发送一封加入邮件列表的电子邮件请求。 等待请求显示在收件箱中。

1. 若要手动启动逻辑应用，请在设计器工具栏中选择“运行”  。 

   如果电子邮件的主题与触发器的主题筛选器匹配，逻辑应用会向你发送审批订阅请求的电子邮件。

1. 在审批电子邮件中，选择“批准”  。

1. 如果订户的电子邮件地址在邮件列表中不存在，逻辑应用会添加该人的电子邮件地址并向你发送一封电子邮件，如以下示例所示：

   ![示例电子邮件 - 成功订阅](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   如果逻辑应用无法添加订户，你会收到一封电子邮件，如下示例所示：

   ![示例电子邮件 - 失败订阅](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   如果没有收到任何电子邮件，请检查电子邮件的垃圾邮件文件夹。 垃圾电子邮件筛选器可能会将这些类型的邮件重定向。 否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现在已创建并运行可以跨 Azure、Microsoft 服务和其他 SaaS 应用集成信息的逻辑应用。

## <a name="clean-up-resources"></a>清理资源

不再需要示例逻辑应用时，请删除包含该逻辑应用和相关资源的资源组。 

1. 在 Azure 主菜单中转到“资源组”，然后选择逻辑应用的资源组。 

1. 在资源组菜单中，选择“概述” > “删除资源组”。   

   ![“概览”>“删除资源组”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 输入资源组名称作为确认，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建逻辑应用，以便管理邮件列表请求的审批。 现在，请了解如何集成 Azure 存储和 Azure Functions 之类的 Azure 服务，以便生成处理和存储电子邮件附件的逻辑应用。

> [!div class="nextstepaction"]
> [处理电子邮件附件](../logic-apps/tutorial-process-email-attachments-workflow.md)

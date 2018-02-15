---
title: "生成处理电子邮件和附件的工作流 - Azure 逻辑应用 | Microsoft Docs"
description: "本教程介绍如何创建自动化工作流，以便使用 Azure 逻辑应用、Azure 存储和 Azure Functions 处理电子邮件和附件"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 210731ce2e792452650b7a92cfc542c78a0e8014
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>使用逻辑应用处理电子邮件和附件

Azure 逻辑应用有助于跨 Azure 服务、Microsoft 服务、其他软件即服务 (SaaS) 应用以及本地系统自动完成工作流和集成数据。 本教程介绍如何生成可以处理传入电子邮件和任何附件的[逻辑应用](../logic-apps/logic-apps-overview.md)。 此逻辑应用可以处理该内容，将内容保存到 Azure 存储，然后发送查看该内容的通知。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 [Azure 存储](../storage/common/storage-introduction.md)和存储资源管理器，以便查看保存的电子邮件和附件。
> * 创建用于从电子邮件中删除 HTML 的 [Azure 函数](../azure-functions/functions-overview.md)。 本教程包括可以用于此函数的代码。
> * 创建空白逻辑应用。
> * 添加一个触发器，用于监视电子邮件中的附件。
> * 添加一个条件，用于检查电子邮件是否有附件。
> * 添加一个操作，用于在电子邮件有附件时调用 Azure 函数。
> * 添加一个操作，用于为电子邮件和附件创建存储 Blob。
> * 添加一个操作，用于发送电子邮件通知。

完成后，逻辑应用看起来大致与以下工作流类似：

![完成的逻辑应用概览](./media/tutorial-process-email-attachments-workflow/overview.png)

如果还没有 Azure 订阅，可以在开始前<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

## <a name="prerequisites"></a>先决条件

* 逻辑应用支持的电子邮件提供商（例如 Office 365 Outlook、Outlook.com 或 Gmail）提供的电子邮件帐户。 至于其他提供商，请[查看此处的连接器列表](https://docs.microsoft.com/connectors/)。

  此逻辑应用使用 Office 365 Outlook 帐户。 
  如果使用其他电子邮件帐户，则常规步骤保持不变，但 UI 显示可能稍有不同。

* 下载并安装<a href="http://storageexplorer.com/" target="_blank">免费 Microsoft Azure 存储资源管理器</a>。 此工具可以用于检查存储容器是否已正确设置。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

使用 Azure 帐户凭据登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。

## <a name="set-up-storage-to-save-attachments"></a>设置用于保存附件的存储。

可以在 [Azure 存储容器](../storage/common/storage-introduction.md)中以 Blob 形式保存传入电子邮件和附件。 

1. 请先使用以下设置[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)，然后才能创建存储容器：

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **Name** | attachmentstorageacct | 存储帐户的名称 | 
   | **部署模型** | 资源管理器 | 用于管理资源部署的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md) | 
   | **帐户种类** | 常规用途 | [存储帐户类型](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **性能** | 标准 | 此设置指定支持的数据类型以及用于存储数据的介质。 请参阅[存储帐户的类型](../storage/common/storage-introduction.md#types-of-storage-accounts)。 | 
   | **复制** | 本地冗余存储 (LRS) | 此设置指定如何复制、存储、管理和同步数据。 请参阅[复制](../storage/common/storage-introduction.md#replication)。 | 
   | **需要安全传输** | 已禁用 | 此设置指定从连接进行请求所需的安全性。 请参阅[需要安全传输](../storage/common/storage-require-secure-transfer.md)。 | 
   | **订阅** | <*your-Azure-subscription-name*> | Azure 订阅的名称 | 
   | **资源组** | LA-Tutorial-RG | 用于组织和管理相关资源的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称。 <p>**注意：**资源组存在于特定的区域。 本教程中的项目可能不在所有区域提供，请尽可能尝试使用同一区域。 | 
   | **位置** | 美国东部 2 | 用于存储存储帐户信息的区域 | 
   | **配置虚拟网络** | 已禁用 | 对于本教程，请保留“禁用”设置。 | 
   |||| 

   也可使用 [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) 或 [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md)。
  
2. 在 Azure 部署存储帐户以后，请获取存储帐户的访问密钥：

   1. 在存储帐户菜单的“设置”下，选择“访问密钥”。 
   2. 找到 **key1**（在“默认密钥”下）和存储帐户名称。

      ![复制并保存存储帐户名称和密钥](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   也可使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list)。 

3. 为电子邮件附件创建存储容器。
   
   1. 在存储帐户菜单的“概览”窗格中，选择“服务”下的“Blob”，然后选择“+ 容器”。

   2. 输入“attachments”作为容器名称。 在“公共访问级别”下选择“容器(对容器和 Blob 进行匿名读取访问)”，然后选择“确定”。

   也可使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create)。 
   完成后，即可在 Azure 门户的存储帐户中找到存储容器，如下所示：

   ![完成的存储容器](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

接下来，将存储资源管理器连接到存储帐户。

## <a name="set-up-storage-explorer"></a>设置存储资源管理器

现在，请将存储资源管理器连接到存储帐户，以便确认逻辑应用是否已正确地在存储帐户中将附件另存为 Blob。

1. 打开 Microsoft Azure 存储资源管理器。 当存储资源管理器提示你连接到 Azure 存储时，请选择“使用存储帐户名称和密钥” > “下一步”。
如果无提示显示，请在资源管理器工具栏中选择“添加帐户”。

2. 在“使用名称和密钥进行附加”下，输入以前保存的存储帐户名称和访问密钥。 选择“下一步” > “连接”。

3. 检查存储帐户和容器是否正确显示在存储资源管理器中：

   1. 在“资源管理器”下，****展开“(本地和附加)”****> 
   “存储帐户”****> **attachmentstorageaccount** > 
   ****“Blob 容器”。

   2. 确认“attachments”容器现在是否显示。 
   例如：

      ![存储资源管理器 - 确认存储容器](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

接下来，创建用于从传入电子邮件中删除 HTML 的 [Azure 函数](../azure-functions/functions-overview.md)。

## <a name="create-a-function-to-clean-html"></a>创建用于清除 HTML 的函数

现在，请使用这些步骤提供的代码片段，创建一个可从每封传入电子邮件中删除 HTML 的 Azure 函数。 这样就可以让电子邮件内容更干净，更容易处理。 然后即可从逻辑应用调用此函数。

1. 请先使用以下设置[创建函数应用](../azure-functions/functions-create-function-app-portal.md)，然后即可创建函数：

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **应用名称** | CleanTextFunctionApp | 函数应用的全局唯一的描述性名称 | 
   | **订阅** | <*your-Azure-subscription-name*> | 以前使用过的同一 Azure 订阅 | 
   | **资源组** | LA-Tutorial-RG | 以前使用过的同一 Azure 资源组 | 
   | **托管计划** | 使用计划 | 此设置决定了如何分配和缩放用于运行函数应用的资源，例如计算能力。 请参阅[托管计划比较](../azure-functions/functions-scale.md)。 | 
   | **位置** | 美国东部 2 | 以前使用过的同一区域 | 
   | **存储** | cleantextfunctionstorageacct | 为函数应用创建存储帐户。 只使用小写字母和数字。 <p>**注意：**此存储帐户包含函数应用，不同于以前创建的用于电子邮件附件的存储帐户。 | 
   | **Application Insights** | 关闭 | 请对 [Application Insights](../application-insights/app-insights-overview.md) 启用应用程序监视，但对于本教程，请保留“禁用”设置。 | 
   |||| 

   如果函数应用没有在部署后自动打开，请在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中找到该应用。 在 Azure 主菜单中选择“应用服务”，然后选择函数应用。

   ![创建的函数应用](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   如果“应用服务”没有显示在 Azure 菜单中，请改为转到“更多服务”。 在搜索框中找到并选择“Function App”。 有关详细信息，请参阅[创建函数](../azure-functions/functions-create-first-azure-function.md)。

   也可使用 [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md) 或 [PowerShell 和资源管理器模板](../azure-resource-manager/resource-group-template-deploy.md)。

2. 在 **Function App** 下展开 **CleanTextFunctionApp**，然后选择“函数”。 在函数工具栏中选择“+ 新建函数”。

   ![创建新的函数](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. 在“选择下面的模板或转到快速入门”下，选择“HttpTrigger - C#”函数模板。

   ![选择函数模板](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. 在“为函数命名”下输入 ```RemoveHTMLFunction```。 在“HTTP 触发器” > “授权级别”下，保留默认的**函数**值，然后选择“创建”。

   ![为函数命名](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. 在编辑器打开后，将模板代码替换为以下代码，以便删除 HTML 并将结果返回给调用方：

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. 完成后，选择“保存”。 若要测试函数，请选择编辑器右边缘箭头 (**<**) 图标下的“测试”。 

   ![打开“测试”窗格。](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. 在“测试”窗格的“请求正文”下，输入下面这行内容，然后选择“运行”。

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![测试函数](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   “输出”窗口显示函数的以下结果：

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

在检查函数正常以后，创建逻辑应用。 虽然本教程介绍的是如何创建从电子邮件中删除 HTML 的函数，但逻辑应用也有“HTML 到文本”连接器。

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 在 Azure 主菜单中选择“新建” > “企业集成” > “逻辑应用”。

   ![创建逻辑应用](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. 在“创建逻辑应用”下，提供有关逻辑应用的信息，如下所示。 完成后，请选择“固定到仪表板” > “创建”。

   ![提供逻辑应用信息](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-ProcessAttachment | 逻辑应用的名称 | 
   | **订阅** | <*your-Azure-subscription-name*> | 以前使用过的同一 Azure 订阅 | 
   | **资源组** | LA-Tutorial-RG | 以前使用过的同一 Azure 资源组 |
   | **位置** | 美国东部 2 | 以前使用过的同一区域 | 
   | **Log Analytics** | 关闭 | 对于本教程，请保留“关闭”设置。 | 
   |||| 

3. 在 Azure 部署应用以后，逻辑应用设计器会打开并显示一个页面，其中包含简介视频以及针对常用逻辑应用模式的模板。 在“模板”下选择“空白逻辑应用”。

   ![选择空白逻辑应用模板](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

接下来，请添加一个[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)，侦听有附件的传入电子邮件。 每个逻辑应用都必须从触发器开始，在发生特定事件或新数据符合特定条件的情况下触发。 有关详细信息，请参阅[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="monitor-incoming-email"></a>监视传入电子邮件

1. 在设计器的搜索框中输入“当电子邮件到达时”。 为电子邮件提供商选择以下触发器：**<*your-email-provider*> - 收到新电子邮件时**，例如：

   ![为电子邮件提供商选择以下触发器：“收到新电子邮件时”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。 
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。 

2. 如果系统要求提供凭据，请登录电子邮件帐户，这样逻辑应用就可以连接到该电子邮件帐户。

3. 现在，请提供触发器用来筛选新电子邮件的条件。

   1. 指定用于检查电子邮件的文件夹、时间间隔和频率。

      ![指定用于检查邮件的文件夹、时间间隔和频率](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | 设置 | 值 | 说明 | 
      | ------- | ----- | ----------- | 
      | **文件夹** | Inbox | 要检查的电子邮件文件夹 | 
      | **间隔** | 1 | 在两次检查之间需等待的时间间隔数 | 
      | **频率** | 分钟 | 两次检查的间隔的时间单位 | 
      |  |  |  | 
  
   2. 选择“显示高级选项”并指定以下设置：

      | 设置 | 值 | 说明 | 
      | ------- | ----- | ----------- | 
      | **带有附件** | 是 | 仅获取带有附件的电子邮件。 <p>**注意：**此触发器不删除帐户中的任何电子邮件，仅检查新邮件，并且仅处理与主题筛选器匹配的电子邮件。 | 
      | **包括附件** | 是 | 获取充当工作流输入的附件，而不是仅仅检查是否有附件。 | 
      | **主题筛选器** | ```Business Analyst 2 #423501``` | 要在电子邮件主题中查找的文本 | 
      |  |  |  | 

4. 若要立即隐藏触发器的详细信息，请单击触发器的标题栏。

   ![折叠形状即可隐藏详细信息](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

   逻辑应用现已生成，但除了检查电子邮件，不能执行任何操作。 
   接下来，请添加一个条件，用于指定继续执行工作流所需的条件。

## <a name="check-for-attachments"></a>检查是否有附件

1. 在该触发器下，选择“+ 新建步骤” > “添加条件”。

   当条件形状显示时，默认情况下，参数列表或动态内容列表就会出现，并显示上一步中任何可以作为工作流输入包括的参数。 
   浏览器宽度决定了哪个列表显示。

2. 重命名此条件，并提供更好的说明。

   1. 在条件的标题栏中，选择**省略号** (**...**) 按钮 >“重命名”。

      例如，如果浏览器处于窄视图状态：

      ![重命名条件](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      如果浏览器处于宽视图状态，且动态内容列表阻止了对省略号按钮的访问，请在条件中选择“添加动态内容”，关闭该列表。 
      
      ![关闭动态内容列表](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. 重命名条件并提供以下说明：```If email has attachments and key subject phrase```

3. 提供一个表达式来说明条件。 

   1. 在条件形状中，选择“在高级模式下编辑”。

      ![在高级模式下编辑条件](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. 在文本框中，输入以下表达式：

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      此表达式将触发器正文（即本教程中的电子邮件）的 **HasAttachment** 属性值与布尔对象 ```True``` 进行比较。 
      如果两个值相等，则电子邮件至少有一个附件，条件通过，工作流继续。

      现在，条件应如下例所示：

      ![条件表达式](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. 选择“在基本模式下编辑”。 表达式现在解析如下：

      ![解析的表达式](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > 若要手动生成表达式，必须在基本模式下工作并让动态列表打开，以便使用表达式生成器。 在“表达式”下，可以选择函数。 在“动态内容”下，可以选择在这些函数中使用的参数字段。
      > 本教程稍后介绍如何手动生成表达式。

4. 保存逻辑应用。

### <a name="test-your-condition"></a>测试条件

现在，请测试条件是否正常工作：

1. 如果逻辑应用尚未运行，请在设计器工具栏中选择“运行”。

   此步骤手动启动逻辑应用，不需等待指定的时间间隔。 
   但在测试性电子邮件到达收件箱之前，什么也不会发生。 

2. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题的文本是在触发器的**主题筛选器**中指定的：```Business Analyst 2 #423501```

   * 电子邮件有一个附件。 
   现在，请直接创建一个空的文本文件，然后将该文件附加到电子邮件。

   当电子邮件到达时，逻辑应用会检查其中是否有附件和指定的主题文本。
   如果满足条件，触发器就会触发，促使逻辑应用引擎创建一个逻辑应用实例并启动工作流。 

3. 若要检查触发器是否已成功触发以及逻辑应用是否已成功运行，请在逻辑应用菜单中选择“概览”。

   ![检查触发器和运行历史记录](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   如果触发器成功触发，但逻辑应用没有触发或运行，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

接下来，定义需要针对 **If true** 分支执行的操作。 若要保存电子邮件及其附件，请从电子邮件正文中删除 HTML，然后在电子邮件和附件的存储容器中创建 Blob。

> [!NOTE]
> 当电子邮件没有附件时，逻辑应用不需针对 **If false** 分支执行任何操作。 完成本教程后，可以针对 **If false** 分支添加需要执行的适当操作，作为附加练习。

## <a name="call-the-removehtmlfunction"></a>调用 RemoveHTMLFunction

1. 在逻辑应用菜单上选择“逻辑应用设计器”。 在 **If true** 分支中，选择“添加操作”。

2. 搜索“azure functions”，然后选择以下操作：**Azure Functions - 选择 Azure 函数**

   ![选择操作“Azure Functions - 选择 Azure 函数”](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. 选择以前创建的函数应用：**CleanTextFunctionApp**

   ![选择 Azure 函数应用](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. 现在请选择函数：**RemoveHTMLFunction**

   ![选择 Azure 函数](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. 重命名函数形状并提供以下说明：```Call RemoveHTMLFunction to clean email body``` 

6. 在函数形状中，输入供函数处理的输入。 指定电子邮件正文，如下所示：

   ![指定函数所需的请求正文](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. 在“请求正文”下输入以下文本： 
   
      ```{ "emailBody": ``` 

      此时会出现关于 JSON 无效的错误，直至你在后续步骤中完成此条目。
      以前测试此函数时，为此函数指定的输入使用 JavaScript 对象表示法 (JSON)。 
      因此，请求正文也必须使用同一格式。 

   2. 在参数列表或动态内容列表的“收到新电子邮件时”下选择“正文”字段。
   在“正文”字段后添加右大括号：```}```

      ![指定要传递到函数的请求正文](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      在逻辑应用定义中，此项以下述格式出现：

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. 保存逻辑应用。

接下来，请添加一项操作，以便在存储容器中创建用于保存电子邮件正文的 Blob。

## <a name="create-blob-for-email-body"></a>创建用于电子邮件正文的 Blob

1. 在 Azure 函数形状下，选择“添加操作”。 

2. 在“选择操作”下搜索“blob”，然后选择以下操作：**Azure Blob 存储 - 创建 Blob**

   ![添加为电子邮件正文创建 Blob 的操作](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. 如果没有到 Azure 存储帐户的连接，请使用如下所示的设置创建到该存储帐户的连接。 完成后，选择“创建”。

   ![创建到存储帐户的连接](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **连接名称** | AttachmentStorageConnection | 连接的描述性名称 | 
   | **存储帐户** | attachmentstorageacct | 以前创建的用于保存附件的存储帐户的名称 | 
   |||| 

4. 重命名“创建 Blob”操作并提供以下说明：```Create blob for email body```

5. 在“创建 Blob”操作中，提供如下所示的信息并选择创建 Blob 所需的相应参数：

   ![提供适用于电子邮件正文的 Blob 信息](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **文件夹路径** | /attachments | 以前创建的容器的路径和名称。 也可通过浏览方式选择容器。 | 
   | **Blob 名称** | “发件人”字段 | 将电子邮件发件人名称作为 Blob 名称传入。 在参数列表或动态内容列表的“收到新电子邮件时”下选择“发件人”。 | 
   | **Blob 内容** | “内容”字段 | 将没有 HTML 的电子邮件正文作为 Blob 内容传入。 在参数列表或动态内容列表的“调用 RemoveHTMLFunction 来清除电子邮件正文”下选择“正文”。 |
   |||| 

6. 保存逻辑应用。 

### <a name="check-attachment-handling"></a>检查附件处理情况

现在，请测试逻辑应用是否采用指定的方式来处理电子邮件：

1. 如果逻辑应用尚未运行，请在设计器工具栏中选择“运行”。

2. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题的文本是在触发器的**主题筛选器**中指定的：```Business Analyst 2 #423501```

   * 电子邮件至少有一个附件。 
   现在，请直接创建一个空的文本文件，然后将该文件附加到电子邮件。

   * 电子邮件的正文中有一些测试内容，例如： 

     ```
     Testing my logic app
     ```

   如果触发器成功触发，但逻辑应用没有触发或运行，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

3. 检查逻辑应用是否已将电子邮件保存到正确的存储容器。 

   1. 在存储资源管理器中展开“(本地和附加)”****> 
   “存储帐户”****>“attachmentstorageacct (外部)”****> 
   “Blob 容器”****> **attachments**。

   2. 检查 **attachments** 容器中是否有电子邮件。 

      此时只有电子邮件显示在容器中，因为逻辑应用尚未处理附件。

      ![检查存储资源管理器中是否有保存的电子邮件](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. 完成后，删除存储资源管理器中的电子邮件。

4. （可选）若要测试 **If false** 分支（目前不执行任何操作），可以发送一封不符合条件的电子邮件。

接下来，添加一个用于处理所有电子邮件附件的循环。

## <a name="process-attachments"></a>处理附件

此逻辑应用使用 **for each** 循环来处理电子邮件中的每个附件。

1. 在“创建用于电子邮件正文的 Blob”形状下，选择“… 更多”，然后选择以下命令：**添加 for each**

   ![添加“for each”循环](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. 重命名循环并提供以下说明：```For each email attachment```

3. 现在指定供循环处理的数据。 单击“从先前的步骤中选择一个输出”框。 从参数列表或动态内容列表中选择“附件”。 

   ![选择“附件”](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   “附件”字段传递一个数组，其中包含电子邮件随附的所有附件。 
   **For each** 循环针对每个通过数组传入的项目重复操作。

4. 保存逻辑应用。

接下来，添加可在 **attachments** 存储容器中将每个附件另存为 Blob 的操作。

## <a name="create-blobs-for-attachments"></a>为附件创建 Blob

1. 在 **For each** 循环中选择“添加操作”，以便指定要对每个已发现附件执行的任务。

   ![添加用于循环的操作](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. 在“选择操作”下搜索“blob”，然后选择以下操作：**Azure Blob 存储 - 创建 Blob**

   ![添加用于创建 Blob 的操作](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. 重命名“创建 Blob 2”操作并提供以下说明：```Create blob for each email attachment```

4. 在“创建适用于每个电子邮件附件的 Blob”操作中，提供如下所示的信息并选择创建每个 Blob 所需的相应参数：

   ![提供 Blob 信息](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **文件夹路径** | /attachments | 以前创建的容器的路径和名称。 也可通过浏览方式选择容器。 | 
   | **Blob 名称** | “名称”字段 | 从参数列表或动态内容列表中选择“名称”，以便传入附件名称作为 Blob 名称。 | 
   | **Blob 内容** | “内容”字段 | 从参数列表或动态内容列表中选择“内容”，以便传入附件内容作为 Blob 内容。 |
   |||| 

5. 保存逻辑应用。 

### <a name="check-attachment-handling"></a>检查附件处理情况

接下来，请测试逻辑应用是否采用指定的方式来处理附件：

1. 如果逻辑应用尚未运行，请在设计器工具栏中选择“运行”。

2. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题的文本是在触发器的**主题筛选器**中指定的：```Business Analyst 2 #423501```

   * 电子邮件至少有两个附件。 
   现在，请直接创建两个空的文本文件，然后将这两个文件附加到电子邮件。

   如果触发器成功触发，但逻辑应用没有触发或运行，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

3. 检查逻辑应用是否已将电子邮件和附件保存到正确的存储容器。 

   1. 在存储资源管理器中展开“(本地和附加)”****> 
   “存储帐户”****>“attachmentstorageacct (外部)”****> 
   “Blob 容器”****> **attachments**。

   2. 检查 **attachments** 容器中是否有电子邮件和附件。

      ![检查是否有已保存的电子邮件和附件](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. 完成后，删除存储资源管理器中的电子邮件和附件。

接下来添加一项操作，使逻辑应用发送需查看附件的电子邮件。

## <a name="send-email-notifications"></a>发送电子邮件通知

1. 在 **if true** 分支的 **For each email attachment** 循环下，选择“添加操作”。 

   ![在“for each”循环下添加操作](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. 在“选择操作”下搜索“发送电子邮件”，然后选择与所需电子邮件提供商相对应的“发送电子邮件”操作。 若要通过筛选操作列表来查找特定的服务，可以先在“连接器”下选择连接器。

   ![为电子邮件提供商选择“发送电子邮件”操作](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。 
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。 

3. 如果系统要求提供凭据，请登录电子邮件帐户，这样逻辑应用就可以创建到该电子邮件帐户的连接了。

4. 重命名“发送电子邮件”操作并提供以下说明：```Send email for review```

5. 提供此操作的信息，然后选择要包括在电子邮件中的字段，如下所示。 若要在编辑框中添加空行，请按 Shift + Enter。  

   例如，如果要使用动态内容列表：

   ![发送电子邮件通知](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   如果在列表中找不到预期的字段，请在动态内容列表中或参数列表末尾的“收到新电子邮件时”旁边选择“查看更多”。

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----- | 
   | **To** | <*recipient-email-address*> | 为进行测试，可以使用自己的电子邮件地址。 | 
   | **主题**  | ```ASAP - Review applicant for position: ``` **主题** | 要包括的电子邮件主题。 在参数列表或动态内容列表的“收到新电子邮件时”下选择“主题”字段。 | 
   | **正文** | ```Please review new applicant:``` <p>```Applicant name: ``` **发件人** <p>```Application file location: ``` **路径** <p>```Application email content: ``` **正文** | 电子邮件正文的内容。 从参数列表或动态内容列表中选择以下字段： <p>-“收到新电子邮件时”下的“发件人”字段 </br>-“创建用于电子邮件正文的 Blob”下的“路径”字段 </br>-“调用 RemoveHTMLFunction 来清除电子邮件正文”下的“正文”字段 | 
   |||| 

   如果碰巧选择了一个包含数组的字段，例如“内容”（即包含附件的数组），设计器会围绕引用该字段的操作自动添加“For each”循环。 
   这样一来，逻辑应用就可以对每个数组项执行该操作。 
   若要删除该循环，请删除数组的字段，将引用操作移至循环外部，选择循环的标题栏中的省略号 (**...**)，然后选择“删除”。
     
6. 保存逻辑应用。 

接下来测试逻辑应用，该应用现在看起来类似于以下示例：

![完成的逻辑应用](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>运行逻辑应用

1. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题的文本是在触发器的**主题筛选器**中指定的：```Business Analyst 2 #423501```

   * 电子邮件有一个或多个附件。 
   可以重复使用上一测试留下的任何空的文本文件。 
   如需更逼真的方案，请附加一个简历文件。

   * 电子邮件正文包含以下文本（可以复制和粘贴）：

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. 运行逻辑应用。 如果成功，逻辑应用会发送一封电子邮件，如以下示例所示：

   ![逻辑应用发送的电子邮件通知](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   如果没有收到任何电子邮件，请检查电子邮件的垃圾邮件文件夹。 
   垃圾电子邮件筛选器可能会将这些类型的邮件重定向。 
   否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现在已创建并运行逻辑应用。该应用可以跨不同的 Azure 服务自动完成任务，并且可以调用某些自定义代码。

## <a name="clean-up-resources"></a>清理资源

不再需要包含逻辑应用和相关资源的资源组时，可将其删除。 在 Azure 主菜单中转到“资源组”，然后选择逻辑应用的资源组。 选择“删除资源组”。 输入资源组名称作为确认，然后选择“删除”。

![删除逻辑应用资源组](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何集成 Azure 存储和 Azure Functions 之类的 Azure 服务，以便创建处理和存储电子邮件附件的逻辑应用。 现在，请详细了解可以用来生成逻辑应用的其他连接器。

> [!div class="nextstepaction"]
> [详细了解适用于逻辑应用的连接器](../connectors/apis-list.md)
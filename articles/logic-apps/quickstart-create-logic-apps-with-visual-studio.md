---
title: 在 Visual Studio 中自动化任务工作流
description: 使用 Azure 逻辑应用和 Visual Studio 创建、计划和运行用于企业集成的重复执行工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 3311d1143c0eca0c2b57e89e98dc22ab14fd9308
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82147721"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>快速入门：使用 Azure 逻辑应用创建自动化任务、流程和工作流 - Visual Studio

可以通过 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 Visual Studio 创建工作流，以便自动完成用于跨企业和组织集成应用、数据、系统和服务的任务和流程。 本快速入门展示了如何通过在 Visual Studio 中创建逻辑应用并将这些应用部署到 Azure 来设计和构建这些工作流。 尽管可以在 Azure 门户中执行这些任务，但在需要将逻辑应用添加到源代码管理、发布不同的版本，以及为不同的部署环境创建 Azure 资源管理器模板时，也可以使用 Visual Studio。

如果不熟悉 Azure 逻辑应用，只希望了解基本概念，则可尝试[关于在 Azure 门户中创建逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 逻辑应用设计器在 Azure 门户和 Visual Studio 中的工作方式类似。

在本快速入门中，你将使用 Visual Studio 创建与 Azure 门户快速入门相同的逻辑应用。 此逻辑应用监视网站的 RSS 源，并针对该源中的每个新项发送电子邮件。 完成后的逻辑应用看起来与以下概略性的工作流类似：

![完成的逻辑应用](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 下载并安装以下工具（如果没有）：

  * [Visual Studio 2019、2017 或 2015 - Community Edition 或更高版本](https://aka.ms/download-visual-studio)。 本快速入门使用 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 安装 Visual Studio 2019 或 2017 时，请务必选择“Azure 开发”工作负荷。 

  * [用于 .NET 的 Microsoft Azure SDK（2.9.1 或更高版本）](https://azure.microsoft.com/downloads/)。 详细了解[用于 .NET 的 Azure SDK](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 适用于所需版本的 Visual Studio 扩展的最新 Azure 逻辑应用工具：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    可以直接从 Visual Studio Marketplace 下载并安装 Azure 逻辑应用工具，或了解[如何从 Visual Studio 内部安装此扩展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。 完成安装后，请务必重启 Visual Studio。

* 使用嵌入式逻辑应用设计器时访问 Web

  设计器需要通过 Internet 连接在 Azure 中创建资源，以及从逻辑应用中的连接器读取属性和数据。 例如，对于 Dynamics CRM Online 连接，设计器会在 CRM 实例中检查默认属性和自定义属性。

* 逻辑应用支持的（例如 Office 365 Outlook、Outlook.com 或 Gmail）电子邮件帐户。 至于其他提供商，请查看[此处的连接器列表](https://docs.microsoft.com/connectors/)。 本示例使用 Office 365 Outlook。 如果使用其他提供商，整个步骤仍然是相同的，但 UI 可能稍有不同。

  > [!IMPORTANT]
  > 如果要使用 Gmail 连接器，则只有 G-Suite 商业帐户可以在逻辑应用中不受限制地使用此连接器。 如果有 Gmail 用户帐户，则只能将此连接器与 Google 批准的特定服务一起使用，也可以[创建用于通过 Gmail 连接器进行身份验证的 Google 客户端应用](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。 有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>创建 Azure 资源组项目

若要开始，请创建 [Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。 详细了解 [Azure 资源组和资源](../azure-resource-manager/management/overview.md)。

1. 启动 Visual Studio。 使用 Azure 帐户登录。

1. 在“文件”  菜单中，选择“新建” > “项目”  。  （键盘：Ctrl + Shift + N）

   ![在“文件”菜单中，选择“新建”>“项目”。](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. 在“已安装”下，选择“Visual C#”或“Visual Basic”    。 选择“云”   >   “Azure 资源组”。 为项目命名，例如：

   ![创建 Azure 资源组项目](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > 资源组名称只能包含字母、数字、句点 (`.`)、下划线 (`_`)、连字符 (`-`) 和括号（`(`、`)`），但不能以句点 (`.`) 结尾  。
   >
   > 如果“云”或“Azure 资源组”未显示，请确保安装 Azure SDK for Visual Studio。  

   如果使用 Visual Studio 2019，请执行以下步骤：

   1. 在“创建新项目”框中，选择适用于 Visual C# 或 Visual Basic 的“Azure 资源组”项目。   选择“**下一页**”。

   1. 提供要使用的 Azure 资源组的名称和其他项目信息。 选择“创建”  。

1. 在模板列表中，选择“逻辑应用”模板。  选择“确定”  。

   ![选择逻辑应用模板](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   在 Visual Studio 创建项目后，解决方案资源管理器会打开并显示你的解决方案。 在你的解决方案中，**LogicApp.json** 文件不仅存储逻辑应用定义，还是一个可以用于部署的 Azure 资源管理器模板。

   ![解决方案资源管理器显示新的逻辑应用解决方案和部署文件](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>创建空白逻辑应用

在创建 Azure 资源组项目后，使用**空白逻辑应用**模板创建你的逻辑应用。

1. 在解决方案资源管理器中，打开 **LogicApp.json** 文件的快捷菜单。 选择“使用逻辑应用设计器打开”  。 （键盘：Ctrl + L）

   ![使用逻辑应用设计器打开 LogicApp.json 文件](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果你的 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 的最新更新。

   Visual Studio 会提示你提供用于为你的逻辑应用和连接创建并部署资源的 Azure 订阅和 Azure 资源组。

1. 对于“订阅”  ，请选择你的 Azure 订阅。 对于“资源组”，请选择“新建”以创建其他 Azure 资源组   。

   ![选择 Azure 订阅、资源组和资源位置](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | 设置 | 示例值 | 说明 |
   | ------- | ------------- | ----------- |
   | 用户帐户 | Fabrikam <br> sophia-owen@fabrikam.com | 登录 Visual Studio 时使用的帐户 |
   | **订阅** | 即用即付 <br> (sophia-owen@fabrikam.com) | Azure 订阅的名称以及关联的帐户 |
   | **资源组** | MyLogicApp-RG <br> （美国西部） | 用于存储和部署逻辑应用资源的 Azure 资源组和位置 |
   | **位置** | **与资源组相同** | 用于部署逻辑应用的位置类型和特定位置。 位置类型可以是 Azure 区域，也可以是现有的[集成服务环境 (ISE)](connect-virtual-network-vnet-isolated-environment.md)。 <p>对于本快速入门，请将“位置类型”设置为“区域”，并将“**位置**”设置为“**与资源组相同**”。 <p>**注意**：创建资源组项目后，可以[更改位置类型和位置](manage-logic-apps-with-visual-studio.md#change-location)，但不同的位置类型会以不同的方式影响逻辑应用。 |
   ||||

1. 逻辑应用设计器会打开一个页面，其中显示了介绍视频和常用的触发器。 向下滚动，越过视频和触发器，找到“模板”  ，然后选择“空白逻辑应用”  。

   ![选择“空白逻辑应用”](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>生成逻辑应用工作流

接下来，添加一个 RSS [触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)，该触发器在出现新的源项时触发。 每个逻辑应用都以触发器开头，该触发器在特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来运行工作流。

1. 在逻辑应用设计器的搜索框下，选择“全部”  。 在搜索框中输入“rss”。 从触发器列表中选择此触发器：**发布源项时**

   ![通过添加触发器和操作来生成逻辑应用](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. 在触发器出现在设计器中后，通过执行 [Azure 门户快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)中的工作流步骤来完成逻辑应用的构建。 完成后，逻辑应用如以下示例所示：

   ![完成的逻辑应用](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. 保存你的 Visual Studio 解决方案。 （键盘：Ctrl + S）

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>将逻辑应用部署到 Azure

必须先将逻辑应用从 Visual Studio 部署到 Azure，然后才能运行并测试逻辑应用。

1. 在解决方案资源管理器的项目快捷菜单中，选择“部署”   >   “新建”。 如果出现系统提示，请使用 Azure 帐户登录。

   ![创建逻辑应用部署](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. 就此部署来说，请保留默认的 Azure 订阅、资源组和其他设置。 选择“部署”。 

   ![将逻辑应用部署到 Azure 资源组](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. 如果“编辑参数”  框出现，请为你的逻辑应用提供一个资源名称。 保存设置。

   ![提供逻辑应用的部署名称](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   部署开始时，应用的部署状态显示在 Visual Studio 的“输出”  窗口中。 如果状态不显示，请打开“显示输出来源”  列表，然后选择 Azure 资源组。

   ![部署状态输出](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   如果所选连接器需要你提供输入，则会在背景中打开一个 PowerShell 窗口，提示你输入必需的密码或机密密钥。 输入该信息后，部署会继续。

   ![PowerShell 窗口](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   在部署完成后，你的逻辑应用会出现在 Azure 门户中，并会根据指定的计划（每隔一分钟）运行。 如果该触发器发现新的源项，则触发器会激发，这将创建运行你的逻辑应用操作的一个工作流实例。 你的逻辑应用会针对每个新项发送电子邮件。 反之，如果触发器未发现新项，则触发器不会激发，而是“跳过”工作流实例化。 逻辑应用会等到下一个间隔过后才进行检查。

   下面是此逻辑应用发送的示例电子邮件。 如果没有收到任何电子邮件，请检查垃圾邮件文件夹。

   ![Outlook 针对每个新的 RSS 项发送电子邮件](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

祝贺你，你已使用 Visual Studio 成功生成并部署了逻辑应用。 若要管理逻辑应用并查看其运行历史记录，请参阅[使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。

## <a name="add-new-logic-app"></a>添加新逻辑应用

如果你有现有的 Azure 资源组项目，则可以使用“JSON 大纲”窗口向该项目添加一个新的空白逻辑应用。

1. 在“解决方案资源管理器”中，打开 `<logic-app-name>.json` 文件。

1. 在“视图”  菜单中，选择“其他窗口”   > “JSON 大纲”  。

1. 若要将资源添加到模板文件，请在“JSON 大纲”窗口顶部选择“添加资源”  。 或者在“JSON 大纲”窗口中，打开“资源”快捷菜单，并选择“添加新资源”   。

   ![“JSON 大纲”窗口](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. 在“添加资源”对话框中，在搜索框中，找到 `logic app` 并选择“逻辑应用”   。 为逻辑应用命名，并选择“添加”  。

   ![添加资源](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>清理资源

完成逻辑应用后，删除包含你的逻辑应用和相关资源的资源组。

1. 使用创建逻辑应用时所使用的帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“资源组”或从任意页面搜索并选择“资源组”   。 选择逻辑应用的资源组。

1. 在“概述”页上，选择“删除资源组”   。 输入资源组名称作为确认，然后选择“删除”。 

   ![“资源组”>“概览”>“删除资源组”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. 从本地计算机删除 Visual Studio 解决方案。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Visual Studio 生成、部署和运行逻辑应用。 若要了解如何使用 Visual Studio 管理和执行逻辑应用的高级部署，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)

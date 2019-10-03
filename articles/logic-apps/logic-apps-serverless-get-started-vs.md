---
title: 在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 生成无服务器应用
description: 生成、 部署和管理第一个无服务器应用的 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444870"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 生成第一个无服务器应用

您可以快速开发和部署云应用使用的无服务器工具和功能在 Azure 中，如[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)并[Azure Functions](../azure-functions/functions-overview.md)。 本文介绍如何在 Visual Studio 中开始生成一个使用逻辑应用来调用 Azure 函数的无服务器应用。 若要详细了解 Azure 中的无服务器解决方案，请参阅[将 Azure Serverless 与 Functions 和逻辑应用配合使用](../logic-apps/logic-apps-serverless-overview.md)。

## <a name="prerequisites"></a>必备组件

若要生成 Visual Studio 中的无服务器应用，需要：

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 以下工具。 如果没有它们，请下载并安装它们。

  * [2017 或 2015 （社区或其他版本） 的 visual Studio 2019](https://aka.ms/download-visual-studio)。 
  本快速入门使用免费的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 安装 Visual Studio 2019 或 2017 时，请务必选择“Azure 开发”工作负荷。 

  * [Microsoft Azure SDK for.NET (2.9.1 版或更高版本)](https://azure.microsoft.com/downloads/)。 
  详细了解[用于 .NET 的 Azure SDK](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)。

  * 适用于 Visual Studio 所需版本的 Azure 逻辑应用工具：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    可以直接从 Visual Studio Marketplace 下载并安装 Azure 逻辑应用工具，或了解[如何从 Visual Studio 内部安装此扩展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。 
    完成安装后，请务必重启 Visual Studio。

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)进行本地调试函数。

* 对使用嵌入式的逻辑应用设计器时 web 的访问权限。

  设计器需要通过 Internet 连接在 Azure 中创建资源，以及从逻辑应用中的连接器读取属性和数据。 
  例如，如果使用 Dynamics CRM Online 连接器，则设计器在 CRM 实例中检查可用的默认属性和自定义属性。

## <a name="create-a-resource-group-project"></a>创建资源组项目

若要开始，请为无服务器应用创建一个 [Azure 资源组项目](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 在 Azure 中，你在中创建资源*资源组*，这是用于组织、 管理和部署整个应用程序作为单个资产的资源的逻辑集合。 对于 Azure 中的无服务器应用，你的资源组包含 Azure 逻辑应用和 Azure Functions 的资源。 详细了解 [Azure 资源组和资源](../azure-resource-manager/resource-group-overview.md)。

1. 启动 Visual Studio，并使用你的 Azure 帐户登录。

1. 在“文件”  菜单中，选择“新建” > “项目”  。 

   ![在 Visual Studio 中创建新项目](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. 在“已安装”下，选择“Visual C#”或“Visual Basic”    。 然后，选择**云** > **Azure 资源组**。

   > [!NOTE]
   > 如果**云**类别或**Azure 资源组**项目不存在，请确保你安装 Azure SDK for Visual Studio。

   如果使用 Visual Studio 2019，请执行以下步骤：

   1. 在中**创建一个新项目**框中，选择**Azure 资源组**项目模板，用于任一 VisualC#或 Visual Basic 中，并选择**下一步**。

   1. 提供的名称和你想要使用 Azure 资源组的其他项目信息。 完成操作后，选择“创建”  。

1. 指定你的项目的名称和位置，然后选择**确定**。

   Visual Studio 会提示从模板列表中选择一个模板。 
   此示例使用 Azure 快速入门模板，以便您可以生成包含逻辑应用并对 Azure 函数的调用的无服务器应用。

   > [!TIP]
   > 如果你不想要将解决方案预先部署到 Azure 资源组，可以使用空白的“逻辑应用”模板，该模板只会创建一个空的逻辑应用。 

1. 从**显示此位置中的模板**列表中，选择**Azure 快速入门 (github.com/Azure/azure-quickstart-templates)** 。

1. 在搜索框中，输入“logic-app”作为筛选器。 从结果中，选择**101-logic-app-and-function-app**模板。

   ![选择 Azure 快速入门模板](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio 将为资源组项目创建并打开一个解决方案。 
   所选的 Azure 快速入门模板创建一个名为 azuredeploy.json 在资源组项目的部署模板。 此部署模板包括一个简单的逻辑应用触发的 HTTP 请求，调用 Azure 函数时，并返回结果作为 HTTP 响应的定义。

   ![新无服务器解决方案](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 接下来，将你的解决方案部署到 Azure。 在打开部署模板和无服务器应用中查看的资源之前必须执行此操作。

## <a name="deploy-your-solution"></a>部署解决方案

可以在 Visual Studio 中的逻辑应用设计器中打开逻辑应用之前，必须具有已在 Azure 中部署的 Azure 资源组。 然后，设计器可与逻辑应用中的资源和服务建立连接。 此任务中，请执行以下步骤来部署到 Azure 门户从 Visual Studio 解决方案：

1. 在解决方案资源管理器中，从资源项目的快捷菜单中选择“部署” > “新建”。  

   ![创建资源组的新部署](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 如果它们尚不存在被选中，选择你的 Azure 订阅和你想要部署的资源组。 然后，选择**部署**。

   ![部署设置](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 如果**编辑参数**框出现，提供要用于逻辑应用并在部署时，Azure function app 的资源名称，然后保存你的设置。 请务必对函数应用使用全局唯一的名称。

   ![提供逻辑应用和函数应用的名称](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   当 Visual Studio 开始部署到指定的资源组时，解决方案的部署状态会显示在 Visual Studio 的“输出”窗口中。  
   部署完成后，逻辑应用将在 Azure 门户中处于活动状态。

## <a name="edit-your-logic-app-in-visual-studio"></a>编辑逻辑应用在 Visual Studio 中

若要在部署后编辑逻辑应用，请通过使用 Visual Studio 中的逻辑应用设计器中打开逻辑应用。

1. 在解决方案资源管理器，从快捷菜单中的 azuredeploy.json 文件中，选择**打开与逻辑应用设计器**。

   ![在逻辑应用设计器中打开 azuredeploy.json](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > 如果在 Visual Studio 2019 中没有此命令，检查你的 Visual Studio 具有最新的更新。

1. 之后**逻辑应用属性**框中显示，在**订阅**，如果没有选中它，请选择你的 Azure 订阅。 下**资源组**，选择资源组和部署你的解决方案的位置，然后选择**确定**。

   ![逻辑应用属性](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   逻辑应用设计器打开后，可以继续添加步骤或更改工作流，并保存更新。

   ![在逻辑应用设计器中打开的逻辑应用](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>创建 Azure Functions 项目

若要使用 JavaScript、 Python、 创建函数项目和函数F#、 PowerShell、 批处理或 Bash，按照中的步骤[使用 Azure Functions Core Tools](../azure-functions/functions-run-local.md)。 若要使用开发 Azure 函数C#在解决方案内使用C#类库中的步骤[发布为函数应用的.NET 类库](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)。

## <a name="deploy-functions-from-visual-studio"></a>从 Visual Studio 部署函数

在部署模板部署 azuredeploy.json 文件中的变量所指定的 Git 存储库从解决方案中有任何 Azure 函数。 如果创建并在解决方案中编写函数项目，您可以签入 Git 源控件 （例如，GitHub 或 Azure DevOps） 到该项目，然后更新`repo`变量，以便该模板部署 Azure 函数。

## <a name="manage-logic-apps-and-view-run-history"></a>管理逻辑应用和查看运行历史记录

对于已在 Azure 中部署的逻辑应用，仍可以编辑、 管理、 查看运行历史记录，并禁用这些应用程序从 Visual Studio。

1. 在 Visual Studio 的“视图”菜单中，选择“Cloud Explorer”。  

1. 下**的所有订阅**，选择与你想要管理，并选择逻辑应用相关联的 Azure 订阅**应用**。

1. 在“逻辑应用”下面，选择自己的逻辑应用。  在该应用的快捷菜单中，选择“使用逻辑应用编辑器打开”  。

   > [!TIP]
   > 如果在 Visual Studio 2019 中没有此命令，检查你的 Visual Studio 具有最新的更新。

现在，可将已发布的逻辑应用下载到资源组项目中。 因此，尽管您可能在 Azure 门户中启动了逻辑应用，可以仍导入，并管理该应用在 Visual Studio 中。 有关详细信息，请参阅[使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio 管理逻辑应用](manage-logic-apps-with-visual-studio.md)

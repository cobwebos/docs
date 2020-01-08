---
title: 在 Visual Studio 中生成第一个无服务器应用
description: 在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 生成、部署和管理无服务器应用
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: a3fcff9f895fba3574bbce976087295018ab51c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428745"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 构建你的第一个无服务器应用

你可以使用 Azure 中的无服务器工具和功能（如[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Azure Functions](../azure-functions/functions-overview.md)）快速开发和部署云应用。 本文介绍如何在 Visual Studio 中开始生成一个使用逻辑应用来调用 Azure 函数的无服务器应用。 若要详细了解 Azure 中的无服务器解决方案，请参阅[将 Azure Serverless 与 Functions 和逻辑应用配合使用](../logic-apps/logic-apps-serverless-overview.md)。

## <a name="prerequisites"></a>必备组件

若要在 Visual Studio 中生成无服务器应用，需要：

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 以下工具。 如果尚未这样做，请下载并安装它们。

  * [Visual Studio 2019、2017或2015（社区版或其他版本）](https://aka.ms/download-visual-studio)。 
  本快速入门使用免费的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 安装 Visual Studio 2019 或 2017 时，请务必选择“Azure 开发”工作负荷。

  * [用于 .NET 的 Microsoft Azure SDK （版本2.9.1 或更高版本）](https://azure.microsoft.com/downloads/)。 
  详细了解[用于 .NET 的 Azure SDK](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)。

  * 适用于 Visual Studio 所需版本的 Azure 逻辑应用工具：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    可以直接从 Visual Studio Marketplace 下载并安装 Azure 逻辑应用工具，或了解[如何从 Visual Studio 内部安装此扩展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。 
    完成安装后，请务必重启 Visual Studio。

  * 用于本地调试函数的[Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 。

* 使用嵌入逻辑应用设计器时访问 web。

  设计器需要通过 Internet 连接在 Azure 中创建资源，以及从逻辑应用中的连接器读取属性和数据。 
  例如，如果使用 Dynamics CRM Online 连接器，则设计器在 CRM 实例中检查可用的默认属性和自定义属性。

## <a name="create-a-resource-group-project"></a>创建资源组项目

若要开始，请为无服务器应用创建一个 [Azure 资源组项目](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 在 Azure 中，在*资源组*中创建资源，这是用于将整个应用的资源作为单个资产进行组织、管理和部署的逻辑集合。 对于 Azure 中的无服务器应用，你的资源组包含 Azure 逻辑应用和 Azure Functions 的资源。 详细了解 [Azure 资源组和资源](../azure-resource-manager/management/overview.md)。

1. 启动 Visual Studio，并使用 Azure 帐户登录。

1. 在“文件”菜单中，选择“新建” > “项目”。

   ![在 Visual Studio 中创建新项目](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. 在“已安装”下，选择“Visual C#”或“Visual Basic”。 然后，选择 "**云** > **Azure 资源组**"。

   > [!NOTE]
   > 如果**云**类别或**azure 资源组**项目不存在，请确保已安装 Azure SDK for Visual Studio。

   如果使用 Visual Studio 2019，请执行以下步骤：

   1. 在 "新建**项目**" 框中，选择 "Visual C# " 或 "Visual Basic" 的 " **Azure 资源组**" 项目模板，然后选择 "**下一步**"。

   1. 提供要用于 Azure 资源组的名称和其他项目信息。 完成操作后，选择“创建”。

1. 为项目指定名称和位置，然后选择 **"确定"** 。

   Visual Studio 会提示你从 "模板" 列表中选择一个模板。 
   此示例使用 Azure 快速入门模板，以便你可以构建包含逻辑应用和 Azure 函数调用的无服务器应用。

   > [!TIP]
   > 在不希望将解决方案 predeploy Azure 资源组的情况下，可以使用 "空白**逻辑应用**" 模板，该模板只会创建一个空的逻辑应用。

1. 从 "**显示此位置的模板**" 列表中，选择 " **Azure 快速入门（github.com/Azure/azure-quickstart-templates）** "。

1. 在搜索框中，输入 "逻辑应用" 作为筛选器。 在结果中，选择 " **101--应用程序和功能" 应用**程序模板。

   ![选择 Azure 快速入门模板](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio 将为资源组项目创建并打开一个解决方案。 
   你选择的 Azure 快速入门模板将在资源组项目中创建一个名为 azuredeploy.json 的部署模板。 此部署模板包括一个简单逻辑应用的定义，该应用由 HTTP 请求触发，调用 Azure 函数，并以 HTTP 响应的形式返回结果。

   ![新无服务器解决方案](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 接下来，将解决方案部署到 Azure。 必须先执行此操作，然后才能打开部署模板并查看无服务器应用的资源。

## <a name="deploy-your-solution"></a>部署解决方案

在 Visual Studio 中的逻辑应用设计器中打开逻辑应用之前，必须已在 Azure 中部署 Azure 资源组。 然后，设计器可与逻辑应用中的资源和服务建立连接。 对于此任务，请执行以下步骤，将解决方案从 Visual Studio 部署到 Azure 门户：

1. 在解决方案资源管理器中，从资源项目的快捷菜单中选择 "**部署** > "**新建**"。

   ![创建资源组的新部署](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 如果尚未选择 Azure 订阅，请选择要部署的 Azure 订阅和资源组。 然后选择 "**部署**"。

   ![部署设置](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 如果显示 "**编辑参数**" 框，请提供要用于逻辑应用的资源名称和部署中的 Azure function app，并保存设置。 请务必对函数应用使用全局唯一的名称。

   ![提供逻辑应用和函数应用的名称](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   当 Visual Studio 开始部署到指定的资源组时，解决方案的部署状态会显示在 Visual Studio 的“输出”窗口中。 
   部署完成后，逻辑应用将在 Azure 门户中处于活动状态。

## <a name="edit-your-logic-app-in-visual-studio"></a>在 Visual Studio 中编辑逻辑应用

若要在部署后编辑逻辑应用，请使用 Visual Studio 中的逻辑应用设计器打开逻辑应用。

1. 在解决方案资源管理器中，从 azuredeploy.json 文件的快捷菜单中，选择 "**打开并逻辑应用设计器**"。

   ![在逻辑应用设计器中打开 azuredeploy.json](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > 如果你的 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 的最新更新。

1. 出现 "**逻辑应用属性**" 框后，在 "**订阅**" 下，选择你的 Azure 订阅（如果尚未选择）。 在 "**资源组**" 下，选择部署解决方案的资源组和位置，然后选择 **"确定"** 。

   ![逻辑应用属性](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   逻辑应用设计器打开后，可以继续添加步骤或更改工作流，并保存更新。

   ![在逻辑应用设计器中打开的逻辑应用](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>创建 Azure Functions 项目

若要使用 JavaScript、Python、 F#、PowerShell、Batch 或 Bash 创建函数项目和函数，请按照[使用 Azure Functions Core Tools](../azure-functions/functions-run-local.md)中的步骤进行操作。 若要在解决方案中使用C#来开发 Azure 函数，请按照C#将[.net 类库作为 Function App 发布](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)中的步骤使用类库。

## <a name="deploy-functions-from-visual-studio"></a>从 Visual Studio 部署函数

你的部署模板将从 azuredeploy.json 文件中的变量指定的 Git 存储库中部署你的解决方案中的任何 Azure 功能。 如果在解决方案中创建和创作函数项目，可以将该项目签入 Git 源控件（例如 GitHub 或 Azure DevOps），然后更新 `repo` 变量，以便该模板部署 Azure 函数。

## <a name="manage-logic-apps-and-view-run-history"></a>管理逻辑应用和查看运行历史记录

对于已部署在 Azure 中的逻辑应用，你仍可以编辑、管理、查看的运行历史记录，并从 Visual Studio 中禁用这些应用。

1. 在 Visual Studio 的“视图”菜单中，选择“Cloud Explorer”。

1. 在 "**所有订阅**" 下，选择与要管理的逻辑应用相关联的 Azure 订阅，然后选择 "**应用**"。

1. 在“逻辑应用”下面，选择自己的逻辑应用。 在该应用的快捷菜单中，选择“使用逻辑应用编辑器打开”。

   > [!TIP]
   > 如果你的 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 的最新更新。

现在，可将已发布的逻辑应用下载到资源组项目中。 因此，虽然你可能已在 Azure 门户中启动了逻辑应用，但仍可以在 Visual Studio 中导入和管理该应用。 有关详细信息，请参阅[使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio 管理逻辑应用](manage-logic-apps-with-visual-studio.md)

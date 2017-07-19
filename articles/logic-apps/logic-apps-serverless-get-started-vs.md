---
title: "在 Visual Studio 中构建无服务器应用 | Microsoft Docs"
description: "参考这篇有关在 Visual Studio 中创建、部署和管理应用的指南开始构建第一个无服务器应用。"
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017

---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>使用逻辑应用和 Functions 在 Visual Studio 中构建无服务器应用

使用 Azure 中的无服务器工具和功能可以快速开发和部署云应用程序。  本文档重点介绍如何在 Visual Studio 中开始构建无服务器应用程序。  [可在此文中找到](logic-apps-serverless-overview.md)有关 Azure 中的无服务器应用程序概述。

## <a name="getting-everything-ready"></a>做好一切准备

下面是从 Visual Studio 构建无服务器应用程序所要满足的先决条件：

* [Visual Studio 2017](https://www.visualstudio.com/vs/) 或 Visual Studio 2015 - Community、Professional 或 Enterprise
* [用于 Visual Studio 的逻辑应用工具](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [最新版 Azure SDK](https://azure.microsoft.com/downloads/)（2.9.1 或更高版本）
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* 用于在本地调试 Functions 的 [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)
* 使用嵌入式逻辑应用设计器时访问 Web

## <a name="getting-started-with-a-deployment-template"></a>部署模板入门

需在资源组中管理 Azure 中的资源。  资源组是资源的逻辑分组。  使用资源组可以部署和管理资源集合。  对于 Azure 中的无服务器应用程序，我们的资源组包含 Azure 逻辑应用和 Azure Functions。  使用 Visual Studio 中的资源组项目，可以单个资产的形式开发、管理和部署整个应用程序。

### <a name="create-a-resource-group-project-in-visual-studio"></a>在 Visual Studio 中创建资源组项目

1. 在 Visual Studio 中，单击“新建项目”添加一个项目
1. 在“云”类别中，选择创建 Azure“资源组”项目  
 * 如果未列出该类别或项目，请确保已安装用于 Visual Studio 的 Azure SDK
1. 指定项目的名称和位置，选择“确定”，然后根据 Visual Studio 的提示选择模板。  可以选择从“空白”模板、逻辑应用或其他资源开始。  但是，在本例中，我们将使用 Azure 快速入门模板开始构建无服务器应用。
1. 通过“Azure 快速入门”
   “选择 Azure 快速入门模板”选择显示模板**** ![][1]
1. 选择无服务器快速入门模板：**101-logic-app-and-function-app**，然后单击“确定”

快速入门模板将在资源组项目中创建部署模板。  该模板包含一个可调用 Azure Functions 并返回结果的简单逻辑应用。  如果在解决方案资源管理器中打开 `azuredeploy.json` 文件，可以看到无服务器应用的资源。

## <a name="deploying-the-serverless-application"></a>部署无服务器应用程序

在 Visual Studio 中打开逻辑应用可视化设计器之前，需要预先部署 Azure 资源组。  这样，设计器便可与逻辑应用中的资源和服务建立连接并使用这种连接。  若要开始，只需部署创建的解决方案。

1. 在 Visual Studio 中右键单击该项目，选择“部署”，然后创建**新的**部署。 ![选择新的资源部署][2]
1. 选择有效的 Azure 订阅和资源组
1. 选择**部署**解决方案
1. 输入逻辑应用和 Azure Function App 的名称。  Azure 函数名称必须全局唯一

无服务器解决方案将部署到指定的资源组。  在 Visual Studio 中的“输出”中可以查看部署状态。

## <a name="editing-the-logic-app-in-visual-studio"></a>在 Visual Studio 中编辑逻辑应用

将解决方案部署到任一资源组后，可以使用可视化设计器来编辑和更改逻辑应用。

1. 在解决方案资源管理器中右键单击 `azuredeploy.json` 文件，然后选择“使用逻辑应用设计器打开”
1. 选择解决方案已部署到的**资源组**和**位置**，然后选择“确定”

现在，逻辑应用可视化设计器应与 Visual Studio 一起显示。  可以继续添加步骤，修改工作流，然后保存更改。  还可以从 Visual Studio 创建逻辑应用。  如果在模板导航器中右键单击“资源”，可以选择将一个**逻辑应用**添加到项目。  空的逻辑应用将载入可视化设计器，而不会预先部署到资源组。

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>管理和查看已部署逻辑应用的运行历史记录

还可以在 Azure 中管理和查看已部署的逻辑应用的运行历史记录。  如果在 Visual Studio 中打开“Cloud Explorer”工具，可以右键单击任一逻辑应用，然后选择编辑、禁用、查看属性或查看运行历史记录。  单击编辑还可将已发布的逻辑应用下载到 Visual Studio 资源组项目中。  这意味着，即使已开始在 Azure 门户中构建逻辑应用，也仍可以从 Visual Studio 导入和管理它。

## <a name="developing-an-azure-function-in-visual-studio"></a>在 Visual Studio 中开发 Azure 函数

部署模板将会针对 `azuredeploy.json` 变量中指定的 git 存储库部署解决方案中包含的所有 Azure 函数。  如果在解决方案中创作了函数项目，请将其签入源代码管理（GitHub、Visual Studio Team Services 等）并更新 `repo` 变量，然后，模板将会部署该 Azure 函数。

### <a name="creating-an-azure-function-project"></a>创建 Azure 函数项目

如果使用 JavaScript、Python、F#、Bash、Batch 或 PowerShell，请遵循 [Functions CLI 中的步骤](../azure-functions/functions-run-local.md)创建项目。  如果在 C# 中开发函数，可对 Azure 函数使用当前解决方案中的 [C# 类库](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)。

## <a name="next-steps"></a>后续步骤

* [了解如何构建无服务器社交仪表板](logic-apps-scenario-social-serverless.md)
* [通过 Visual Studio Cloud Explorer 管理逻辑应用](logic-apps-manage-from-vs.md)
* [逻辑应用工作流定义语言](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png


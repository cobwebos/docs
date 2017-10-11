---
title: "生成 Visual Studio 中的无服务器应用 |Microsoft 文档"
description: "开始使用本指南上创建、 部署和管理 Visual Studio 中的应用程序对第一个无服务器应用程序使用。"
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
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>生成无服务器应用程序在 Visual Studio 中使用逻辑应用和函数

无服务器工具和功能在 Azure 中的允许进行快速开发和部署云应用程序。  本文档重点介绍如何开始在 Visual Studio 中生成无服务器应用程序。  概述在 Azure 中无服务器[可这篇文章中找到](logic-apps-serverless-overview.md)。

## <a name="getting-everything-ready"></a>准备的所有内容

下面是生成 Visual Studio 中的无服务器应用程序所需的先决条件：

* [Visual Studio 2017](https://www.visualstudio.com/vs/)或 Visual Studio 2015-Community、 Professional 或 Enterprise
* [逻辑应用 Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更高版本)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Azure 函数核心工具](https://www.npmjs.com/package/azure-functions-core-tools)调试请函数本地
* 访问 web 时使用嵌入的逻辑应用程序设计器

## <a name="getting-started-with-a-deployment-template"></a>开始使用部署模板

在 Azure 中管理资源进行资源组中。  资源组是资源的逻辑分组。  资源组允许部署和管理的资源的集合。  无服务器应用程序在 Azure，我们资源组包含 Azure 逻辑应用和 Azure 函数。  通过使用 Visual Studio 中的资源组项目，我们就能够开发、 管理和部署整个应用程序作为单个资产。

### <a name="create-a-resource-group-project-in-visual-studio"></a>在 Visual Studio 中创建资源组项目

1. 在 Visual Studio 中，单击以添加**新项目**
1. 在**云**类别中，选择要创建 Azure**资源组**项目  
 * 如果你看不见类别或项目列出，请确保你有 Azure SDK for Visual Studio 安装
1. 为项目提供的名称和位置，并选择**确定**创建 Visual Studio 会提示要选择模板。  你可以选择要启动从留空，则开头逻辑应用或其他资源。  但是，在这种情况下我们使用 Azure 快速入门模板来获取我们入门无服务器应用程序。
1. 选择此项可显示模板来源**Azure 快速入门**![选择 Azure 快速入门模板][1]
1. 选择无服务器的快速入门模板： **101-logic-app-and-function-app**单击**确定**

快速入门模板在资源组项目中创建部署模板。  该模板包含简单的逻辑应用来调用 Azure 函数，并返回结果。  如果你打开`azuredeploy.json`文件在解决方案资源管理器，你可以看到无服务器应用程序的资源。

## <a name="deploying-the-serverless-application"></a>将无服务器应用程序部署

你可以在 Visual Studio 中打开逻辑应用可视化设计器之前，需要有预部署的 Azure 资源组。  这允许设计器中创建和使用逻辑应用资源和服务的连接。  若要开始，我们只需部署创建的解决方案。

1. 右键单击该项目在 Visual Studio 中，选择**部署**，并创建**新建**部署![选择新的资源部署][2]
1. 选择一个有效的 Azure 订阅和资源组
1. 选择**部署**解决方案
1. 输入逻辑应用和 Azure 函数应用的名称。  Azure 函数名称确实需要是全局唯一

无服务器的解决方案部署到指定的资源组。  如果你看一下**输出**Visual Studio 中，你可以查看部署的状态。

## <a name="editing-the-logic-app-in-visual-studio"></a>编辑逻辑应用，Visual Studio 中

解决方案部署到的任何资源组后，可以使用可视化设计器来编辑，并对逻辑应用进行更改。

1. 右键单击`azuredeploy.json`文件在解决方案资源管理器，然后选择**打开与逻辑应用设计器**
1. 选择**资源组**和**位置**解决方案已部署到并选择**确定**

逻辑应用可视化设计器现在应可以看到与 Visual Studio。  你可以继续添加步骤，修改工作流，并保存更改。  你还可以从 Visual Studio 创建逻辑应用。  如果你右键单击**资源**在模板导航器中，你可以选择添加**逻辑应用**到项目。  在没有可视化设计器中加载的空 logic apps 预先部署到资源组。

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>管理和查看已部署的逻辑应用的运行历史记录

你也可以管理和查看部署在 Azure 中的逻辑应用的运行历史记录。  如果你打开**云资源管理器**工具可以在 Visual Studio 中，右键单击任何逻辑应用并选择编辑、 禁用、 查看属性，或查看运行历史记录。  单击编辑还可以将已发布的逻辑应用下载到 Visual Studio 资源组项目。  这意味着，即使你开始构建您在 Azure 门户中的逻辑应用，你可以仍将其导入和管理它从 Visual Studio。

## <a name="developing-an-azure-function-in-visual-studio"></a>开发 Visual Studio 中的 Azure 函数

部署模板部署的解决方案中指定的 git 存储库中包含任何 Azure 函数`azuredeploy.json`变量。  如果创作解决方案中的函数项目，将其签入源代码管理 （GitHub、 Visual Studio Team Services 等） 和更新`repo`变量，模板将会部署 Azure 函数。

### <a name="creating-an-azure-function-project"></a>创建 Azure 函数项目

如果使用 JavaScript、 Python、 F #、 Bash、 批处理或 PowerShell，请按照[函数 CLI 中的步骤](../azure-functions/functions-run-local.md)创建项目。  如果开发 C# 中的函数，则可以使用[C# 类库](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)Azure 函数的当前解决方案中。

## <a name="next-steps"></a>后续步骤

* [了解如何构建无服务器的社交仪表板](logic-apps-scenario-social-serverless.md)
* [从 Visual Studio 云资源管理器管理的逻辑应用](logic-apps-manage-from-vs.md)
* [逻辑应用程序工作流定义语言](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png

---
title: "方案 - 使用 Azure Serverless 创建客户见解仪表板 | Microsoft Docs"
description: "以示例方式说明如何生成仪表板，以便使用 Azure 逻辑应用和 Azure Functions 管理客户反馈、社交数据等。"
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
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: 0b6e118cb13ab8185d8eeb42bec6147155967967
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>使用 Azure 逻辑应用和 Azure Functions 创建实时客户见解仪表板

Azure Serverless 工具功能强大，可以在云中快速生成和托管应用程序，不需考虑基础结构问题。  在本方案中，我们将创建一个仪表板，以便针对客户反馈触发相关操作、通过机器学习分析反馈，以及通过 Power BI 或 Azure Data Lake 之类的源发布见解。

## <a name="overview-of-the-scenario-and-tools-used"></a>所用方案和工具概述

为了实现此解决方案，我们将利用 Azure 中无服务器应用的两大组件：[Azure Functions](https://azure.microsoft.com/services/functions/) 和 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)。

逻辑应用是云中的无服务器工作流引擎。  它可以跨无服务器组件提供业务流程，还可以连接到 100 多个服务和 API。  就本方案来说，我们将创建一个逻辑应用，以便在客户提供反馈时触发相应操作。  部分有助于对客户反馈进行响应的连接器包括：Outlook.com、Office 365、Survey Monkey、Twitter，以及[来自 Web 窗体](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/)的 HTTP 请求。  对于以下工作流，我们将监视 Twitter 上的井号标签。

Functions 在云中提供无服务器计算。  在此方案中，我们将使用 Azure Functions 根据一系列预定义的关键字来标记客户的推文。

整个解决方案可以[在 Visual Studio 中生成](logic-apps-deploy-from-vs.md)，并[作为资源模板的一部分部署](logic-apps-create-deploy-template.md)。  [第 9 频道](http://aka.ms/logicappsdemo)也有此方案的视频演练。

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>生成可以针对客户数据进行触发操作的逻辑应用

在 Visual Studio 或 Azure 门户中[创建逻辑应用](logic-apps-create-a-logic-app.md)后，请执行以下操作：

1. 针对 Twitter 的“出现新推文时”添加一个触发器
2. 将触发器配置为侦听推文的关键字或井号标签。

   > [!NOTE]
   > 触发器的“重复周期”属性将决定逻辑应用以多高的频率检查基于轮询的触发器上出现的新项

   ![Twitter 触发器示例][1]

现在，只要出现新推文，此应用就会触发。  然后，我们就可以根据该推文数据，对所表达的观点进行更深入的了解。  为此，我们使用 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)来检测文本观点。

1. 单击“新步骤”
1. 选择或搜索“文本分析”连接器
1. 选择“检测观点”操作
1. 如果系统提示，请为文本分析服务提供有效的认知服务密钥
1. 添加“推文文本”作为要分析的文本。

有了推文数据以及对推文的见解以后，即可使用许多其他的相关连接器：
* Power BI - 向流式处理数据集添加行：在 Power BI 仪表板上实时查看推文。
* Azure Data Lake - 追加文件：将客户数据添加到要包括在分析作业中的 Azure Data Lake 数据集。
* SQL - 添加行：将数据存储在数据库中，方便以后检索。
* Slack - 发送消息：提醒 Slack 通道存在负面反馈，需要采取措施。

也可使用 Azure 函数根据数据进行更多自定义计算。

## <a name="enriching-the-data-with-an-azure-function"></a>使用 Azure 函数来丰富数据的内涵

创建函数之前，需在 Azure 订阅中设置函数应用。  若要详细了解如何在门户中创建 Azure 函数，可单击[此处](../azure-functions/functions-create-first-azure-function-azure-portal.md)

若要直接从逻辑应用调用函数，需为其设置 HTTP 触发器绑定。  建议使用 **HttpTrigger** 模板。

在此方案中，Azure 函数的请求正文将是推文文本。  在函数代码中，直接定义用于确定推文文本是否包含某个关键字或短语的逻辑。  可以根据方案需要来决定函数本身的简单或复杂程度。

在函数末尾，直接通过某些数据为逻辑应用返回响应。  该响应可以是简单的布尔值（例如 `containsKeyword`），也可以是复杂的对象。

![配置的 Azure 函数步骤][2]

> [!TIP]
> 从逻辑应用中的函数访问复杂响应时，请使用“分析 JSON”操作。

保存该函数之后，即可将其添加到在上面创建的逻辑应用中。  在逻辑应用中，请执行以下操作：

1. 以单击方式添加“新步骤”
1. 选择 **Azure Functions** 连接器
1. 选择从现有函数中进行选择，并浏览到所创建的函数。
1. 为“请求正文”递送“推文文本”

## <a name="running-and-monitoring-the-solution"></a>运行和监视解决方案

在逻辑应用中创作无服务器业务流程时，一大优势是调试和监视功能很丰富。  可以通过 Visual Studio、Azure 门户或 REST API 和 SDK 查看任何运行（当前的或历史上的）。

若要测试逻辑应用，一种最简单的方式是使用设计器中的“运行”按钮。  单击“运行”将每隔 5 秒持续对触发器进行轮询，直到检测到事件，并在运行继续的情况下提供实时视图。

可以在 Azure 门户的“概览”边栏选项卡上查看以前的运行历史记录，也可以使用 Visual Studio Cloud Explorer 来进行相同的操作。

## <a name="creating-a-deployment-template-for-automated-deployments"></a>创建自动部署的部署模板

开发出解决方案以后，即可将其捕获并通过 Azure 部署模板部署到世界上的任何 Azure 区域。  这既可用于针对此工作流的不同版本修改参数，也可用于将此解决方案集成到生成和发布管道中。  有关如何创建部署模板的详细信息，可参阅[此文](logic-apps-create-deploy-template.md)。

也可将 Azure Functions 整合到部署模板中，将整个解决方案和所有依赖项作为单个模板进行管理。  [Azure 快速入门模板存储库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)中提供了函数部署模板的示例。

## <a name="next-steps"></a>后续步骤

* [查看 Azure 逻辑应用的其他示例和方案](logic-apps-examples-and-scenarios.md)
* [观看有关如何创建此端到端解决方案的视频演练](http://aka.ms/logicappsdemo)
* [了解如何在逻辑应用中处理和捕获异常](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
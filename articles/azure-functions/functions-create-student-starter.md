---
title: 使用面向学生的 Azure 入门版创建函数
description: 了解如何在面向学生的 Azure 入门版订阅中创建 Azure 函数
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 04/29/2020
ms.author: alkarche
ms.openlocfilehash: ffb6378d3dc4cc3fb23ea62157aad393d8ae6642
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122805"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>使用面向学生的 Azure 入门版创建函数

在本教程中，我们将在面向学生的 Azure 入门版的订阅中创建一个 "hello world" HTTP 函数。 此外，我们还将演练 Azure Functions 在此类订阅中的功能。

借助面向学生的 Microsoft Azure 入门版，可以免费体验在云中进行开发所需的 Azure 产品。  [在此处详细了解此产品/服务。](https://azure.microsoft.com/offers/ms-azr-0144p/)

Azure Functions 用于在[无服务器](https://azure.microsoft.com/solutions/serverless/)环境中执行代码，无需先创建 VM 或发布 Web 应用程序。 [在此处详细了解 Functions。](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>创建函数

 本文介绍如何使用 Azure Functions 在 Azure 门户中创建 "hello world" HTTP 触发器函数。

![在 Azure 门户中创建 Function App](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-function-app"></a>创建函数应用

必须使用 Function App 托管函数的执行。 函数应用可将函数分组为逻辑单元，以便更轻松地管理、部署、缩放和共享资源。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

接下来，在新的 Function App 中创建一个函数。

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>创建 HTTP 触发器函数

1. 从 "**函数**" 窗口的左侧菜单中，选择 "**函数**"，然后从顶部菜单中选择 "**添加**"。 
 
1. 在 "**新建函数**" 窗口中，选择 " **Http 触发器**"。

    ![选择 HTTP 触发器函数](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. 在 "**新建函数**" 窗口中，接受**new 函数**的默认名称，或输入新名称。 

1. 从 "**授权级别**" 下拉列表中选择 "**匿名**"，然后选择 "**创建函数**"。

    Azure 创建 HTTP 触发器函数。 现在，可以通过发送 HTTP 请求来运行新函数了。

## <a name="test-the-function"></a>测试函数

1. 在新的 HTTP 触发器函数中，从左侧菜单中选择 "**代码 + 测试**"，然后从顶部菜单中选择 "**获取函数 URL** "。

    ![选择获取函数 URL](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. 在 "**获取函数 URL** " 对话框中，从下拉列表中选择 "**默认**"，然后选择 "**复制到剪贴板**" 图标。 

    ![从 Azure 门户复制函数 URL](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. 将函数 URL 粘贴到浏览器的地址栏中。 将查询字符串值添加 `?name=<your_name>` 到此 URL 的末尾，然后按 enter 运行该请求。 

    以下示例显示了浏览器中的响应：

    ![浏览器中的函数响应。](./media/functions-create-student-starter/function-app-browser-testing.png)

    请求 URL 包含通过 HTTP 访问函数默认所需的密钥。

1. 运行函数时，会在日志中写入跟踪信息。 若要查看跟踪输出，请返回到门户中的 "**代码 + 测试**" 页，然后展开页面底部的 "**日志**" 箭头。

   ![Azure 门户中的“函数日志”查看器。](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>面向学生的 Azure 入门版支持的功能

在面向学生的 Azure 入门版中，可以访问 Azure Functions 运行时的大部分功能，其中包含以下几个重要的限制：

* HTTP 触发器是唯一受支持的触发器类型。
    * 支持所有输入和所有输出绑定！ [在此处查看完整列表。](functions-triggers-bindings.md)
* 支持的语言： 
    * C# (.NET Core 2)
    * JavaScript（Node.js 8 和 Node.js 10）
    * F# (.NET Core 2)
    * [在此处查看更高级计划中支持的语言](supported-languages.md)
* Windows 是唯一受支持的操作系统。
* 缩放限制为每天最长运行 60 分钟的[一个免费层实例](https://azure.microsoft.com/pricing/details/app-service/windows/)。 收到 HTTP 流量时，会自动将 serverlessly 从0缩放到1个实例，但再也不会。
* 仅支持 [版本 2.x 和更高版本](functions-versions.md)的 Functions 运行时。
* 支持使用所有开发人员工具来编辑和发布函数。 这包括 VS Code、Visual Studio、Azure CLI 和 Azure 门户。 如果你想要使用门户以外的任何内容，则需要先在门户中创建一个应用，然后在首选工具中选择该应用作为部署目标。

## <a name="next-steps"></a>后续步骤

现在已完成使用简单的 HTTP 触发器函数创建函数应用。 接下来，你可以浏览本地工具、更多语言、监视和集成。

 * [使用 Visual Studio 创建你的第一个函数](./functions-create-your-first-function-visual-studio.md)
 * [使用 Visual Studio Code 创建你的第一个函数](./functions-create-first-function-vs-code.md)
 * [Azure Functions JavaScript 开发人员指南](./functions-reference-node.md)
 * [使用 Azure Functions 连接到 Azure SQL 数据库](./functions-scenario-database-table-cleanup.md)
 * [详细了解 Azure Functions HTTP 绑定](./functions-bindings-http-webhook.md)。
 * [监视 Azure Functions](./functions-monitoring.md)

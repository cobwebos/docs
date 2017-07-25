---
title: "在 Azure 中使用 Visual Studio 创建你的第一个函数 | Microsoft Docs"
description: "使用 Azure Functions Tools for Visual Studio 创建一个简单的 HTTP 触发的函数并将其发布到 Azure。"
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions, 事件处理, 计算, 无服务器体系结构"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/05/2017
ms.author: rachelap, glenga
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: be7a9979ba7e6aa26c60b24bcc892ca35af3c1fc
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="create-your-first-function-using-visual-studio"></a>使用 Visual Studio 创建你的第一个函数

Azure Functions 可让你在无服务器环境中执行代码，而无需先创建 VM 或发布 Web 应用程序。

> [!IMPORTANT]
> 本主题使用 Visual Studio 预览版完成所述的步骤。 在继续下一步之前，请确保已安装 [Visual Studio 2017 预览版 15.3](https://www.visualstudio.com/vs/preview/)。

本主题介绍如何使用 Azure Function Tools for Visual Studio 2017 在本地创建并测试“hello world”函数。 然后将函数代码发布到 Azure。

![Visual Studio 项目中的 Azure Functions 代码](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>先决条件

若要完成本教程，请安装：

* [Visual Studio 2017 预览版 15.3](https://www.visualstudio.com/vs/preview/)，包括 **Azure 开发**工作负荷。

    ![使用 Azure 开发工作负荷安装 Visual Studio 2017](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-functions-tools-for-visual-studio-2017"></a>安装 Azure Functions Tools for Visual Studio 2017

在开始之前，必须下载并安装 Azure Functions Tools for Visual Studio 2017。 这些工具只能用于 Visual Studio 2017 预览版 15.3 或更高版本。 如果已安装 Azure Functions Tools，则可跳过此部分。

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## <a name="create-an-azure-functions-project-in-visual-studio"></a>在 Visual Studio 中创建 Azure Functions 项目

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

创建项目以后，即可创建你的第一个函数。

## <a name="create-the-function"></a>创建函数

在“解决方案资源管理器”中，右键单击项目节点，然后选择“添加” > “新建项”。 选择 **Azure Function**，然后单击“添加”。

选择 **HttpTrigger**，键入“函数名称”，针对“访问权限”选择“匿名”，然后单击“创建”。 创建的函数可以通过 HTTP 请求从任何客户端访问。 

![创建新的 Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

创建 HTTP 触发的函数以后，即可在本地计算机上对其进行测试。

## <a name="test-the-function-locally"></a>在本地测试函数

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

从 Azure Functions 运行时输出复制函数的 URL。  

![Azure 本地运行时](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 将查询字符串 `&name=<yourname>` 追加到此 URL 并执行请求。 下面演示浏览器中函数返回的对本地 GET 请求的响应： 

![浏览器中的函数 localhost 响应](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

若要停止调试，请单击 Visual Studio 工具栏中的“停止”按钮。

验证该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 可以直接从 Visual Studio 创建函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>在 Azure 中测试函数

从“发布”配置文件页复制函数应用的基 URL。 将 URL 的 `localhost:port` 部分（在本地测试函数时使用）替换为新的基 URL。 与前面一样，请确保将查询字符串 `&name=<yourname>` 追加到此 URL 并执行请求。

调用 HTTP 触发的函数的 URL 如下所示：

    http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 下面演示浏览器中函数返回的对远程 GET 请求的响应： 

![浏览器中的函数响应](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>后续步骤

你已使用简单的 HTTP 触发函数通过 Visual Studio 创建 C# 函数应用。 

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

若要详细了解如何使用 Azure Functions Core Tools 进行本地测试和调试，请参阅[在本地进行 Azure Functions 的编码和测试](functions-run-local.md)。 若要详细了解如何将函数作为 .NET 类库进行开发，请参阅[搭配使用 Azure Functions 和 .Net 类库](functions-dotnet-class-library.md)。 



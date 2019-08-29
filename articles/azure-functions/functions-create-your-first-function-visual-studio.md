---
title: 在 Azure 中使用 Visual Studio 创建你的第一个函数
description: 使用 Visual Studio 创建并发布一个 HTTP 触发的 Azure 函数。
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
keywords: azure functions, functions, 事件处理, 计算, 无服务器体系结构
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 9a2978bc181fd4c50665b6178d22d19eeb9e2568
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096574"
---
# <a name="create-your-first-function-using-visual-studio"></a>使用 Visual Studio 创建你的第一个函数

Azure Functions 用于在[无服务器](https://azure.microsoft.com/solutions/serverless/)环境中执行代码，无需先创建 VM 或发布 Web 应用程序。

本文介绍如何使用 Visual Studio 2019 在本地创建和测试“hello world”函数，然后将其发布到 Azure。 本快速入门专为 Visual Studio 2019 设计。 使用 Visual Studio 2017 创建 Functions 项目时，必须先安装[最新的 Azure Functions 工具](functions-develop-vs.md#check-your-tools-version)。

![浏览器中的函数 localhost 响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须先安装 [Visual Studio 2019](https://azure.microsoft.com/downloads/)。 确保还安装了 **Azure 开发**工作负荷。

![安装包含“Azure 开发”工作负载的 Visual Studio](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>创建函数应用项目

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio 将创建一个项目和一个包含 HTTP 触发器函数类型样本代码的类。 方法中的 `FunctionName` 属性设置函数的名称，默认情况下为 `HttpTrigger`。 `HttpTrigger` 属性指定该函数将由某个 HTTP 请求触发。 样本代码发送 HTTP 响应，其中包含请求正文或查询字符串中的值。

通过将适当的属性应用于方法，可以使用输入和输出绑定来扩展函数的功能。 有关详细信息，请参阅 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)的[触发器和绑定](functions-dotnet-class-library.md#triggers-and-bindings)部分。

创建函数项目和 HTTP 触发的函数后，可以在本地计算机上对其进行测试。

## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio 与 Azure Functions Core Tools 集成，方便你使用完整的 Functions 运行时在本地测试函数。  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

验证该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 Visual Studio 发布会在你首次发布项目时为你创建一个函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>在 Azure 中测试函数

1. 从“发布”配置文件页复制函数应用的基 URL。 将 URL 的 `localhost:port` 部分（在本地测试函数时使用）替换为新的基 URL。 与前面一样，请确保将查询字符串 `?name=<YOUR_NAME>` 追加到此 URL 并执行请求。

    调用 HTTP 触发函数的 URL 应采用以下格式：

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. 将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 下面演示浏览器中函数返回的对远程 GET 请求的响应：

    ![浏览器中的函数响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>后续步骤

你已通过 Visual Studio 在 Azure 中创建和发布使用简单的 HTTP 触发函数的 C# 函数应用。 若要了解有关以 .NET 类库开发函数的详细信息，请参阅 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)。

> [!div class="nextstepaction"]
> [将 Azure 存储队列绑定添加到函数](functions-add-output-binding-storage-queue-vs.md)

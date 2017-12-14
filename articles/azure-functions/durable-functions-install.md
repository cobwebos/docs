---
title: "安装 Durable Functions 扩展和示例 - Azure"
description: "了解如何针对门户开发或 Visual Studio 开发安装 Azure Functions 的 Durable Functions 扩展。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 58f23406f20f6b0e75ec65197e4cebb6e4e788ac
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>安装 Durable Functions 扩展和示例 (Azure Functions)

Azure Functions 的 [Durable Functions](durable-functions-overview.md) 扩展是在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 中提供的。 本文展示了如何为以下开发环境安装包和一组示例：

* Visual Studio 2017（推荐） 

* Azure 门户

## <a name="visual-studio-2017"></a>Visual Studio 2017

在开发使用 Durable Functions 的应用方面，Visual Studio 当前提供了最佳体验。  函数可以在本地运行，也可以发布到 Azure。 可以从空项目开始，也可以从一组示例函数开始。

### <a name="prerequisites"></a>先决条件

* 安装[最新版本的 Visual Studio](https://www.visualstudio.com/downloads/)（15.3 版或更高版本）。 在安装选项中包括 **Azure 开发**工作负荷。

### <a name="start-with-sample-functions"></a>从示例函数开始

1. 下载 [Visual Studio 的示例应用 .zip 文件](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip)。 不需要添加 NuGet 引用，因为示例项目中已包含它。
2. 安装并运行 [Azure 存储模拟器](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2 版或更高版本。 另外，也可以使用实际的 Azure 存储连接字符串更新 *local.appsettings.json* 文件。
3. 在 Visual Studio 2017 中打开项目。 
4. 若要获得有关如何运行示例的说明，请首先查看[函数链接 - Hello 序列示例](durable-functions-sequence.md)。 示例可以在本地运行，也可以发布到 Azure。

### <a name="start-with-an-empty-project"></a>从空项目开始
 
按照与从示例开始相同的说明执行操作，但请执行以下步骤而非下载 *.zip* 文件：

1. 创建一个 Function App 项目。
2. 将以下 NuGet 包引用添加到 *.csproj* 文件：

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 提供一种涵盖所有主要平台（Windows、macOS 和 Linux）的本地开发体验。  函数可以在本地运行，也可以发布到 Azure。 可以从空项目开始，也可以从一组示例函数开始。

### <a name="prerequisites"></a>先决条件

* 安装[最新版本的 Visual Studio Code](https://code.visualstudio.com/Download) 

* 遵照[在本地对 Azure Functions 进行编程和测试](https://docs.microsoft.com/azure/azure-functions/functions-run-local)中“安装 Azure Functions Core Tools”下面的说明操作

    >[!IMPORTANT]
    > 如果已安装 Azure Functions 跨平台工具，请将其更新到最新可用版本。

*  安装并运行 [Azure 存储模拟器](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2 版或更高版本。 另外，也可以使用实际的 Azure 存储连接更新 *local.appsettings.json* 文件。 


### <a name="start-with-sample-functions"></a>从示例函数开始

1. 克隆 [Durable Functions 存储库](https://github.com/Azure/azure-functions-durable-extension.git)。
2. 在计算机上导航到 [C# 脚本示例文件夹](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx)。 
3. 在命令提示/终端窗口中运行以下命令，安装 Azure Functions Durable Extension：

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
4. 运行 Azure 存储仿真器，或使用实际的 Azure 存储连接字符串更新 *local.appsettings.json* 文件。
3. 在 Visual Studio Code 中打开项目。 
5. 若要获得有关如何运行示例的说明，请首先查看[函数链接 - Hello 序列示例](durable-functions-sequence.md)。 示例可以在本地运行，也可以发布到 Azure。
6. 在命令提示/终端中运行以下命令以启动项目：
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>从空项目开始
 
1. 在命令提示/终端中导航到用于承载函数应用的文件夹。
2. 在命令提示/终端窗口中运行以下命令，安装 Azure Functions Durable Extension：

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
3. 运行以下命令创建函数应用项目：

    ```bash
    func init
    ``` 
4. 运行 Azure 存储仿真器，或使用实际的 Azure 存储连接字符串更新 *local.appsettings.json* 文件。
5. 接下来，运行以下命令创建新函数，然后遵循向导中的步骤操作：

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > 目前尚未提供持久函数模板，但可以先从支持的某个选项着手，然后修改代码。 用于引用[业务流程客户端](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart)、[业务流程触发器](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence)和[活动触发器](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence)的示例。

6. 在 Visual Studio Code 中打开项目文件夹，然后修改模板代码以继续。 
7. 在命令提示/终端中运行以下命令以启动项目：
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure 门户

如果你愿意，可以使用 Azure 门户进行 Durable Functions 开发。

### <a name="create-an-orchestrator-function"></a>创建一个业务流程协调程序函数

1. 在 [functions.azure.com](https://functions.azure.com/signin) 上创建一个新的函数应用。

2. 对该函数应用进行配置以[使用 2.0 运行时版本](functions-versions.md)。

3. 选择“创建自己的自定义函数”创建一个新函数。

4. 将“语言”更改为“C#”，将“方案”更改为“Durable Functions”，选择“Durable Functions Http 初学者 - C#”模板。

5. 在“未安装的扩展”下，单击“安装”来从 NuGet.org 下载扩展。 

6. 安装完成后，选择“Durable Functions Http 初学者 - C#”模板继续创建业务流程客户端函数“HttpStart”。

7. 现在，通过“Durable Functions 业务流程协调程序 - C#”模板创建业务流程函数“HelloSequence”。

8. “Durable Functions 活动 - C#”模板中的最后一个函数名为“Hello”。

9. 转到“HttpStart”函数并复制其 URL。

10. 使用 Postman 或 cURL 调用持久函数。 测试之前，请将 URL 中的 **{functionName}** 替换为业务流程协调程序函数的名称 - **HelloSequence**。  不需要提供数据，只需使用 POST 谓词。 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. 然后，调用“statusQueryGetUri”终结点，随后会看到持久函数的当前状态

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. 继续调用“statusQueryGetUri”终结点，直到状态更改为“Completed” 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

祝贺你！ 现已在 Azure 门户中启动并运行第一个持久函数！

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行函数链接示例](durable-functions-sequence.md)

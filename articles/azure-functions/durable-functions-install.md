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
ms.openlocfilehash: 03326304e438f3b716c43d268f07d8e22cd15ea3
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
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

## <a name="azure-portal"></a>Azure 门户

如果你愿意，可以使用 Azure 门户进行 Durable Functions 开发。

### <a name="create-an-orchestrator-function"></a>创建一个业务流程协调程序函数

1. 在 [functions.azure.com](https://functions.azure.com/signin) 上创建一个新的函数应用。
2. 对该函数应用进行配置以[使用 2.0 运行时版本](functions-versions.md)。
3. 选择“C#”作为语言，选择“所有”作为方案，创建一个新的函数。 然后选择“Durable Functions 业务流程协调程序 - C#”模板。
4. 在“未安装的扩展”下，单击“安装”来从 NuGet.org 下载扩展。

### <a name="copy-sample-code-to-the-function-app"></a>将示例代码复制到函数应用

1. 下载 [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) 文件。
2. 使用 Kudu 或 FTP 将示例文件解压缩到函数应用中的 `D:\home\site\wwwroot`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行函数链接示例](durable-functions-sequence.md)

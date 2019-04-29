---
title: 手动安装或更新 Azure Functions 绑定扩展
description: 了解如何为已部署的函数应用安装或更新 Azure Functions 绑定扩展。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, 函数, 绑定扩展, NuGet, 更新
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: cda977ba59070c3ddaac05784277d6c0b5109f0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035706"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>从门户手动安装或更新 Azure Functions 绑定扩展

Azure Functions 版本 2.x 运行时使用绑定扩展来实现触发器和绑定的代码。 NuGet 包中提供了绑定扩展。 注册一个扩展，实际上是安装一个包。 在开发函数时，安装绑定扩展的方式取决于开发环境。 有关详细信息，请参阅触发器和绑定文章中的[注册绑定扩展](./functions-bindings-register.md)。

有时，需要在 Azure 门户中手动安装或更新绑定扩展。 例如，可能需要将已注册的绑定更新为一个较新的版本。 可能还需要注册一个无法在门户的“集成”选项卡中安装的支持的绑定。

## <a name="install-a-binding-extension"></a>安装绑定扩展

使用以下步骤从门户手动安装或更新扩展。

1. 在 [Azure 门户](https://portal.azure.com)中，找到你的函数应用并选择它。 选择“概述”选项卡，然后选择“停止”。  停止函数应用可解锁文件，以便进行更改。

1. 选择“平台功能”选项卡，然后在“开发工具”下选择“高级工具(Kudu)”。 将在新窗口中打开 Kudu 终结点 (`https://<APP_NAME>.scm.azurewebsites.net/`)。

1. 在 Kudu 窗口中，选择“调试控制台” > “CMD”。  

1. 在命令窗口中，导航到 `D:\home\site\wwwroot`，然后选择 `bin` 旁边的删除图标，以删除该文件夹。 选择“确定”以确认删除。

1. 选择 `extensions.csproj` 文件旁边的编辑图标，该文件定义函数应用的绑定扩展。 项目文件将在在线编辑器中打开。

1. 在“ItemGroup”中对“PackageReference”项进行必要的添加和更新，然后选择“保存”。 可以在[我需要哪些软件包？](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki 文章中找到支持软件包版本的当前列表。 所有三个 Azure 存储绑定都需要 Microsoft.Azure.WebJobs.Extensions.Storage 包。

1. 从 `wwwroot` 文件夹，运行以下命令以重新生成 `bin` 文件夹中引用的程序集。

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. 返回门户的“概述”选项卡，选择“启动”以重启函数应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

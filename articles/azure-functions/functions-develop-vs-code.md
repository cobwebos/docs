---
title: 使用 Visual Studio Code 开发 Azure Functions |Microsoft Docs
description: 了解如何开发和测试 Azure Functions 通过用于 Visual Studio Code 的 Azure Functions 扩展。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452691"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>使用 Visual Studio Code 开发 Azure Functions

[适用于 Visual Studio Code 的 Azure Functions 扩展]，可以本地开发和部署到 Azure 的函数。 如果这是你第一次体验 Azure Functions，可以在 [Azure Functions 简介](functions-overview.md)中了解详细信息。

Azure Functions 扩展提供以下优势： 

* 在本地开发计算机上编辑、生成和运行函数。 
* 将 Azure Functions 项目直接发布到 Azure。 
* 让所有的 Visual Studio Code 的优势的同时用各种语言编写函数。 

该扩展可用于 Azure Functions 版本 2.x 运行时支持以下语言： 

* [C#编译](functions-dotnet-class-library.md) 
* [C#脚本](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>要求你[设置C#作为默认项目语言脚本](#c-script-projects)。

在本文中，示例目前仅适用于 JavaScript (Node.js) 和C#类的库函数。  

本文提供了有关如何使用 Azure Functions 扩展开发函数并将其发布到 Azure 的详细信息。 在阅读本文之前，您应该[创建第一个函数使用 Visual Studio Code](functions-create-first-function-vs-code.md)。

> [!IMPORTANT]
> 不要将本地开发和门户开发混合在同一函数应用中。 从本地项目发布到函数应用时，部署过程会覆盖在门户中开发的任何函数。

## <a name="prerequisites"></a>必备组件

安装和运行之前[Azure Functions 扩展][适用于 Visual Studio Code 的 Azure Functions 扩展]，必须满足以下要求：

* [Visual Studio Code](https://code.visualstudio.com/)之一上安装[支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)。

* 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

你的订阅中创建其他资源的需要如 Azure 存储帐户，当您[使用 Visual Studio Code 进行发布](#publish-to-azure)。

> [!IMPORTANT]
> 可以在本地开发函数，并将其发布到 Azure 而无需启动并在本地运行它们。 没有针对你功能在本地运行，包括自动下载的 Azure Functions Core Tools 的其他要求。 若要了解详细信息，请参阅[其他要求，以本地方式运行](#additional-requirements-to-run-locally)。 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>创建 Azure Functions 项目

函数扩展允许你创建函数应用项目，以及第一个函数。 以下步骤说明如何在新的 functions 项目中创建 HTTP 触发的函数。 [HTTP 触发器](functions-bindings-http-webhook.md)是最简单的函数触发器模板来演示。

1. 从 **Azure：Functions** 区域中，选择“创建函数”图标。

    ![创建函数](./media/functions-develop-vs-code/create-function.png)

1. 为函数应用项目，选择文件夹，然后**选择一种语言的函数项目**。 

1. 选择**HTTP 触发器**函数模板，也可以选择向**暂时跳过**创建不带函数的项目。 你始终可以[向项目添加一个函数](#add-a-function-to-your-project)在更高版本时。 

    ![选择 HTTP 触发器模板](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 类型`HTTPTrigger`的函数名称，然后按 Enter，然后选择**函数**授权。 此授权级别要求你提供[的功能键](functions-bindings-http-webhook.md#authorization-keys)调用函数终结点时。

    ![选择函数身份验证](./media/functions-develop-vs-code/create-function-auth.png)

    此时将使用 HTTP 触发的函数的模板，以所选语言创建函数。

    ![Visual Studio Code 中的 HTTP 触发的函数模板](./media/functions-develop-vs-code/new-function-full.png)

项目模板中所选语言创建项目，安装必需的依赖项。 适用于任何语言，新项目具有以下文件：

* **host.json**：用于配置 Functions 主机。 在本地和 Azure 中运行时，都会应用这些设置。 有关详细信息，请参阅 [host.json 参考](functions-host-json.md)。

* **local.settings.json**：维护本地运行函数时使用的设置。 在本地运行时，仅使用这些设置。 有关详细信息，请参阅[本地设置文件](#local-settings-file)。

    >[!IMPORTANT]
    >由于 local.settings.json 文件可能包含机密，因此必须将其从项目源代码管理中排除。

此时，您可以将输入和输出绑定到由函数[修改 function.json 文件](#javascript-2)，或通过[添加到参数C#类库函数](#c-class-library-2)。

此外可以[向项目添加新函数](#add-a-function-to-your-project)。

## <a name="install-binding-extensions"></a>安装绑定扩展

除 HTTP 和计时器触发器绑定扩展包中实现。 必须安装的触发器和绑定需要它们的扩展包。 安装绑定扩展的方式取决于你项目的语言。

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\#类库

运行[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)命令在终端窗口中安装所需在项目中的扩展包。 下面的示例会安装 Azure 存储扩展，它实现的 Blob、 队列和表存储绑定。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>将函数添加到项目

使用预定义的函数触发器模板之一，可以向现有项目添加一个新的函数。 若要添加新的函数触发器，请按 F1 键打开命令面板中，然后搜索并运行该命令**Azure Functions:创建函数...** .按照提示选择触发器类型和定义触发器的所需的属性。 如果触发器需要用于连接到服务的访问密钥或连接字符串，将其准备就绪之前创建的函数触发器。 

此操作的结果取决于你项目的语言：

### <a name="javascript"></a>JavaScript

在项目中，其中包含新的 function.json 文件和新的 JavaScript 代码文件创建一个新文件夹。

### <a name="c-class-library"></a>C\#类库

一个新C#类库 (.cs) 文件添加到你的项目。

## <a name="add-input-and-output-bindings"></a>添加输入和输出绑定

可以通过添加输入和输出绑定来扩展你的函数。 执行此操作的方式取决于你项目的语言。 若要了解有关绑定的详细信息，请参阅[Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。 

下面的示例连接到名为的存储队列`outqueue`，其中的存储帐户的连接字符串中设置`MyStorageConnection`local.settings.json 中的应用程序设置。 

### <a name="javascript"></a>JavaScript

Visual Studio Code 中，可以将绑定添加到 function.json 文件中，通过遵循一组方便的提示。 若要创建的绑定中，右键单击 （Ctrl + 单击在 macOS 上的）`function.json`文件函数文件夹中，然后选择**添加绑定...** . 

![将绑定添加到现有的 JavaScript 函数 ](media/functions-develop-vs-code/function-add-binding.png)

以下是示例提示定义一个新存储输出绑定：

| Prompt | 值 | 描述 |
| -------- | ----- | ----------- |
| **选择绑定方向** | `out` | 该绑定是输出绑定。 |
| **选择绑定方向...** | `Azure Queue Storage` | 该绑定是 Azure 存储队列绑定。 |
| **用来在代码中标识此绑定的名称** | `msg` | 用于标识代码中引用的绑定参数的名称。 |
| **将向其发送消息队列** | `outqueue` | 绑定要写入到的队列的名称。 如果 *queueName* 不存在，首次使用绑定时，它会创建该属性。 |
| **选择"local.setting.json"中设置的设置** | `MyStorageConnection` | 包含存储帐户的连接字符串的应用程序设置的名称。 `AzureWebJobsStorage` 设置包含连同函数应用一起创建的存储帐户的连接字符串。 |

在此示例中，以下绑定添加到`bindings`function.json 文件中的数组：

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

此外可以直接向你 function.json 添加相同的绑定定义。

在函数代码中，`msg`绑定访问从`context`，如下面的示例：

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

若要了解详细信息，请参阅[队列存储输出绑定](functions-bindings-storage-queue.md#output---javascript-example)引用。

### <a name="c-class-library"></a>C\#类库

更新要添加到以下参数的函数方法`Run`方法定义：

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

此代码需要添加以下`using`语句：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg`参数是`ICollector<T>`完成类型，它表示消息写入到一个输出绑定时该函数的集合。 您添加一个或多个消息到集合中，该函数完成时发送到队列。

若要了解详细信息，请参阅[队列存储输出绑定](functions-bindings-storage-queue.md#output---c-example)引用。

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>发布到 Azure

使用 Visual Studio Code 可以将函数项目直接发布到 Azure。 在此过程中，将在 Azure 订阅中创建函数应用和相关的资源。 函数应用为函数提供了执行上下文。 该项目将打包并部署到 Azure 订阅中的新函数应用。

在发布时从 Visual Studio Code，使用两种部署方法之一：

* [Zip 部署与运行中的包启用](functions-deployment-technologies.md#zip-deploy)： 用于大多数 Azure Functions 部署。
* [外部包 URL](functions-deployment-technologies.md#external-package-url)： 用于部署到 Linux 应用上[消耗计划](functions-scale.md#consumption-plan)。

### <a name="quick-function-app-creation"></a>创建快速函数应用

默认情况下，Visual Studio Code 会自动生成所需的函数应用的 Azure 资源的值。 这些值基于您选择的函数应用名称。 使用默认值为将项目发布到 Azure 中的新函数应用的示例，请参阅[Visual Studio Code 的快速入门文章](functions-create-first-function-vs-code.md#publish-the-project-to-azure)。

如果你想要提供显式名称创建的资源，则必须启用发布使用高级的选项。

### <a name="enabled-publishing-with-advanced-create-options"></a>使用高级已启用的发布创建选项

为您提供对设置控制与相关联的创建 Azure 函数应用中，更新 Azure Functions 扩展以启用高级的设置。

1. 单击**文件 > 首选项 > 设置**

1. 通过导航**用户设置 > 扩展 > Azure 函数**

1. 选中的复选框**Azure 函数：高级的创建**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>将发布到 Azure 中使用高级创建的新函数应用

以下步骤将你的项目发布到新创建的 function app 创建使用高级选项。

1. 在“Azure：函数”** 区域中，选择“部署到函数应用”图标。

    ![函数应用程序设置](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 如果未登录，则系统会提示你**登录到 Azure**。 还可以**创建免费 Azure 帐户**。 在成功从浏览器登录后，返回到 Visual Studio Code。

1. 如果你有多个订阅，请为函数应用**选择一个订阅**，然后选择“+ 在 Azure 中创建新的函数应用”。

1. 以下提示操作，提供以下信息：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | 在 Azure 中选择函数应用 | + 在 Azure 中创建新的 Function App | 在下一步的提示符中，键入用于标识新 function app 的全局唯一名称，然后按 Enter。 函数应用名称的有效字符包括 `a-z`、`0-9` 和 `-`。 |
    | 选择操作系统 | Windows | 函数应用在 Windows 上运行 |
    | 选择宿主计划 | 消耗量计划 | 无服务器[消耗计划托管](functions-scale.md#consumption-plan)使用。 |
    | 选择新的应用程序的运行时 | 您项目的语言 | 在运行时必须匹配你要发布的项目。 |
    | 选择新的资源的资源组 | 创建新资源组 | 在下一步的提示符中，键入资源组名称，如`myResourceGroup`，然后按 enter。 此外可以选择现有资源组。 |
    | 选择存储帐户 | 新建存储帐户 | 在下一步的提示符下，新的存储帐户的类型的全局唯一名称由 function app 和按 Enter 键。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 此外可以选择现有帐户。 |
    | 选择新的资源的位置 | region | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)中的位置。 |

    创建函数应用并应用了部署包之后，会显示一个通知。 在此通知中选择“查看输出”以查看创建和部署结果，其中包括你创建的 Azure 资源。

## <a name="republish-project-files"></a>重新发布项目文件

当您设置[连续部署](functions-continuous-deployment.md)，函数应用在 Azure 中的更新时在连接的源位置更新源文件。 尽管我们建议此开发实践，还可以重新发布 Visual Studio Code 中的在项目文件更新。 

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。

1. 在 Visual Studio Code，请按 F1 打开命令面板。 在命令面板中，搜索并选择`Azure Functions: Deploy to function app...`。

1. 如果未登录，则系统会提示你**登录到 Azure**。 在成功从浏览器登录后，返回到 Visual Studio Code。 如果有多个订阅**选择一个订阅**，其中包含函数应用。

1. 在 Azure 中选择现有的函数应用。 注意有关覆盖函数应用中的所有文件，选择**部署**确认警告并继续。 

此项目是重新生成、 重新打包，并上传到 Azure。 现有项目替换为新的包和函数应用重启。

## <a name="get-deployed-function-url"></a>获取已部署的函数 URL

若要能够调用 HTTP 触发的函数，需要部署到 function app 的函数的 URL。 此 URL 包括任何所需[功能键](functions-bindings-http-webhook.md#authorization-keys)。 可以使用扩展来获取这些 Url 的已部署函数。

1. 按 F1 键打开命令面板中，然后搜索并运行该命令**Azure Functions:复制函数 URL**。

1. 按照提示进行 Azure，然后选择你想要调用的特定 HTTP 触发器中选择函数应用。 

将 URL 复制到剪贴板，以及任何所需的密钥使用传递的函数`code`查询参数。 使用 HTTP 工具提交 POST 请求或浏览器的 GET 请求到远程函数。  

## <a name="run-functions-locally"></a>在本地运行函数

Azure Functions 扩展允许你在本地开发计算机上运行 functions 项目。 本地运行时的运行时承载在 Azure 中的 function app。 从读取本地设置[local.settings.json 文件](#local-settings-file)。

### <a name="additional-requirements-to-run-locally"></a>若要在本地运行的其他要求

若要能够运行 Functions 项目本地，你还必须满足以下附加要求：

* 安装 [Azure Functions Core Tools](functions-run-local.md#v2) 版本 2.x。 下载并安装 Core Tools 包将自动启动本地的项目。 Core Tools 包括整个 Azure Functions 运行时，以便下载和安装可能需要一些时间。

* 针对所选语言安装特定必需组件：

    | 语言 | 要求 |
    | -------- | --------- |
    | **C#** | [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Java 扩展的调试器](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6+](https://www.python.org/downloads/)|

    <sup>*</sup>活动和维护 LTS 版本 （8.11.1 和 10.14.1 建议）。

### <a name="configure-the-project-to-run-locally"></a>将项目配置为在本地运行

Functions 运行时为 HTTP 和 webhook 以外的所有触发器类型在内部使用的 Azure 存储帐户。 这意味着，必须设置**Values.AzureWebJobsStorage**密钥为有效的 Azure 存储帐户连接字符串。

本部分使用[适用于 Visual Studio Code 的 Azure 存储扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)与[Microsoft Azure 存储资源管理器](https://storageexplorer.com/)以连接到并检索存储连接字符串。   

若要设置存储帐户连接字符串，请执行以下操作：

1. 在 Visual Studio 中，打开“Cloud Explorer”，展开“存储帐户” > “你的存储帐户”，然后选择“属性面板”并复制“主连接字符串”值。

2. 在项目内，打开 local.settings.json 项目文件，并将“AzureWebJobsStorage”键的值设置为复制的连接字符串。

3. 重复上述步骤，将唯一键添加到函数所需的其他任何连接的 **Values** 数组。

有关详细信息，请参阅[本地设置文件](#local-settings-file)。

### <a name="debugging-functions-locally"></a>本地调试函数  

若要调试你的函数，按 F5。 如果你尚未下载[Core Tools][Azure Functions Core Tools]，系统会提示你执行此操作。 当安装 Core Tools，且运行，请在终端中显示输出。 这是与运行相同`func host start`来自终端中，但具有额外的核心工具命令生成任务和连接的调试器。  

运行项目时，可以根据需要部署到 Azure 时触发函数。 在调试模式下运行时，按预期在 Visual Studio Code 中命中断点。

HTTP 触发器请求 URL 显示在终端中的输出中。 在本地运行时不使用 HTTP 触发器的功能键。 有关详细信息，请参阅[在 Azure Functions 中测试代码的策略](functions-test-a-function.md)。  

若要了解详细信息，请参阅[使用 Azure Functions Core Tools][Azure Functions Core Tools]。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

默认情况下，将项目发布到 Azure 时，这些设置不会自动迁移。 发布完成后，你可以在 Azure 中发布到函数应用 local.settings.json 中的设置的选项。 若要了解详细信息，请参阅[发布应用程序设置](#publish-application-settings)。

**ConnectionStrings** 中的值永远不会发布。

函数应用程序设置值，还可以在代码中为环境变量读取。 有关详细信息，请参阅这些特定于语言的参考文章的环境变量部分：

* [预编译 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 脚本 (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>在 Azure 中的应用程序设置

在项目中 local.settings.json 文件中的设置应与在 Azure 中的函数应用中的应用程序设置相同。 您将添加到 local.settings.json 的任何设置必须也添加到 Azure 中的函数应用。 发布项目时，不会自动上传这些设置。 同样，在函数应用中创建的任何设置[门户中](functions-how-to-use-azure-function-app-settings.md#settings)必须下载到本地项目。

### <a name="publish-application-settings"></a>发布应用程序设置

将所需的设置发布到 Azure 中的函数应用的最简单方法是使用**上传设置**成功发布你的项目后，将显示的链接。

![部署已完成上传应用程序设置](./media/functions-develop-vs-code/upload-app-settings.png)

此外可以通过使用发布设置`Azure Functions: Upload Local Setting`命令在命令面板中。 各项设置通过使用添加到在 Azure 中的应用程序设置`Azure Functions: Add New Setting...`命令。 

> [!TIP]
> 请确保保存 local.settings.json 文件，然后将其发布。

如果本地文件进行加密，它是解密、 发布，并且重新加密。 如果使用不同的值，在这两个位置中存在的设置，需要选择如何继续。

查看中的现有应用程序设置**Azure:函数**区域中通过展开你的订阅，function app 中，并**应用程序设置**。

![在 Visual Studio Code 中查看函数应用设置](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>从 Azure 下载设置

如果已在 Azure 中创建应用程序设置，可以先将它们下载到 local.settings.json 文件。 通过使用`Azure Functions: Download Remote Settings...`命令。 

与上传，如果本地文件已加密，它是解密、 更新和重新加密。 如果使用不同的值，在这两个位置中存在的设置，需要选择如何继续。

## <a name="monitoring-functions"></a>监视函数

当您[在本地运行](#run-functions-locally)，日志数据传输到 Terminal 控制台。 在 Azure 中的函数应用中运行 functions 项目时，还可以获取日志数据。 您可以连接到流式传输日志在 Azure 中查看近实时日志数据，或者可以更好的函数应用的方式运行理解为启用 Application Insights。

### <a name="streaming-logs"></a>流式处理日志

开发应用程序时，以近乎实时的方式查看日志记录信息通常很有用。 您可以查看正在生成的函数的日志文件的流。 以下输出是举例说明流式传输日志对 HTTP 请求触发的函数：

![流式处理日志输出的 HTTP 触发器](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

若要了解详细信息，请参阅[流式传输日志](functions-monitoring.md#streaming-logs)。 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 流式处理日志仅支持单个实例的 Functions 主机。 当你的函数扩展到多个实例时，其他实例中的数据不会显示在日志流。 [实时指标 Stream](../azure-monitor/app/live-stream.md)在 Application Insights does 支持多个实例。 虽然还在接近实时，流分析还基于[采样的数据](functions-monitoring.md#configure-sampling)。

### <a name="application-insights"></a>Application Insights

监视函数执行的建议方法是将函数应用与 Azure Application Insights 集成。 在 Azure 门户中创建函数应用时，默认情况下会为你完成此集成。 但是，在 Visual Studio 发布期间创建函数应用时，Azure 中的函数应用集成未完成。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

## <a name="c-script-projects"></a>C\#脚本项目

默认情况下，所有C#项目将创建为[C#编译的类库项目](functions-dotnet-class-library.md)。 如果改为想要使用C#脚本项目，必须选择C#脚本作为在 Azure Functions 扩展插件设置中的默认语言。

1. 单击**文件 > 首选项 > 设置**。

1. 通过导航**用户设置 > 扩展 > Azure 函数**。

1. 选择**C #Script**从**Azure 函数：项目语言**。

此时，对基础的核心工具所做的调用包括`--csx`选项，它生成和发布C#脚本 (.csx) 项目文件。 使用指定的默认语言，创建所有项目默认为C#脚本项目。 不需要时设置的默认选择项目语言。 若要创建其他语言项目，必须更改此设置或删除用户 settings.json 文件中。 删除此设置后，会再次要求若要创建项目时选择的语言。

## <a name="command-palette-reference"></a>命令控制板参考

Azure Functions 扩展提供与你在 Azure 中的函数应用进行交互的 Azure 区域中的一个有用的图形界面。 相同的功能也是用于在命令面板 (F1) 命令。 可用的 Azure 函数特定于命令如下：

|Azure Functions 命令  | 描述  |
|---------|---------|
|**添加新的设置...**  |  在 Azure 中创建新的应用程序设置。 若要了解详细信息，请参阅[发布应用程序设置](#publish-application-settings)。 您可能还需要[下载到你的本地设置的此设置](#download-settings-from-azure)。 |
| **配置部署源...** | 在 Azure 中的将函数应用连接到本地 Git 存储库。 若要了解详细信息，请参阅[Azure Functions 的连续部署](functions-continuous-deployment.md)。 |
| **连接到 GitHub 存储库...** | 将函数应用的 GitHub 存储库连接。 |
| **复制函数 URL** | 获取远程 URL 的 HTTP 触发的函数在 Azure 中运行。 若要了解详细信息，请参阅如何[获取已部署的函数 URL](#get-deployed-function-url)。 |
| **在 Azure 中创建函数应用...** | 在 Azure 中的订阅中创建新的 function app。 若要了解详细信息，请参阅如何[将发布到 Azure 中的新函数应用](#publish-to-azure)。        |
| **解密设置** | 用于解密[本地设置](#local-settings-file)通过使用已加密的`Azure Functions: Encrypt Settings`。  |
| **删除函数应用...** | 从 Azure 中的订阅中删除一个现有 function app。 当应用服务计划中有没有其他应用程序时，你将获得过删除的选项。 不删除其他资源，例如存储帐户和资源组。 若要删除所有资源，则应改为[删除资源组](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)。 在本地项目不受影响。 |
|**删除函数...**  | 从 Azure 中的函数应用中删除现有的函数。 由于此删除操作不会影响在本地项目，而是请考虑删除本地函数，然后[将项目重新发布](#republish-project-files)。 |
| **删除代理...** | 从 function app 在 Azure 中删除 Azure Functions 代理。 若要了解有关代理的详细信息，请参阅[使用 Azure Functions 代理](functions-proxies.md)。 |
| **删除设置...** | 删除 Azure 中的函数应用程序设置。 不会影响在 local.settings.json 文件中的设置。 |
| **断开与存储库的连接...**  | 删除[连续部署](functions-continuous-deployment.md)Azure 中的函数应用和源代码控制存储库之间的连接。 |
| **下载远程设置...** | 从 Azure 中的所选的函数应用到 local.settings.json 文件下载设置。 如果本地文件进行加密，它是解密、 更新和重新加密。 如果使用不同的值，在这两个位置中存在的设置，需要选择如何继续。 请确保你已保存的更改到 local.settings.json 文件运行此命令之前。 |
| **编辑设置...** | 在 Azure 中的现有函数应用程序设置的值更改。 不会影响在 local.settings.json 文件中的设置。  |
| **加密设置** | 加密中的各个项`Values`数组[本地设置](#local-settings-file)。 在此文件中，`IsEncrypted`也设置为`true`，它将告诉本地运行时，使用它们之前解密设置。 加密本地设置，以降低的有价值的信息，正在泄漏风险。 在 Azure 中，应用程序设置存储始终加密。 |
| **现在执行函数** | 启动[计时器触发的函数](functions-bindings-timer.md)在 Azure 中手动针对测试目的。 若要了解有关触发非 HTTP 函数在 Azure 中的详细信息，请参阅[手动运行的非 HTTP 触发函数](functions-manually-run-non-http.md)。 |
| **使用 VS Code 用于初始化项目...** | 将所需的 Visual Studio Code 项目文件添加到现有的函数项目。 使用此命令以使用使用核心工具创建的项目。 |
| **更新 Azure Functions Core Tools 的安装** | 安装或更新[Azure Functions Core Tools] ，用于在本地运行。 |
| **Redeploy**  | 允许您重新部署到 Azure 中的特定部署已连接的 Git 存储库的项目文件。 若要重新发布 Visual Studio Code 中，从本地更新[重新发布项目](#republish-project-files)。 |
| **重命名设置...** | 更改 Azure 中的现有函数应用程序设置的键名。 不会影响在 local.settings.json 文件中的设置。 应重命名 Azure 中的设置后[将这些更改下载到本地项目](#download-settings-from-azure)。 |
| **重新启动** | 将函数应用在 Azure 中重新启动。 部署更新也会重启函数应用。 |
| **设置 AzureWebJobStorage...**| 设置的值`AzureWebJobStorage`应用程序设置。 此设置所需的 Azure 函数，并在 Azure 中创建函数应用设置。 |
| **启动** | 在 Azure 中启动已停止的 function app。 | 
| **启动流式处理日志** | 在 Azure 中启动函数应用的流日志。 使用远程故障排除过程在 Azure 中如果你需要查看此信息在近乎实时的流式处理日志。 若要了解详细信息，请参阅[流式传输日志](#streaming-logs)。 |
| **Stop** | 关闭 Azure 中运行的函数应用。 |
| **停止流式传输日志** | 停止 Azure 中的函数应用的流日志。 |
| **切换槽设置为** | 启用时，可确保应用程序设置仍然存在给定的部署槽。 |
| **卸载 Azure Functions 核心工具** | 删除扩展插件所需 Azure Functions Core Tools。 |
| **上传本地设置...** | 将上传到 Azure 中的所选的函数应用你 local.settings.json 文件中的设置。 如果本地文件进行加密，它是解密、 上传，并且重新加密。 如果使用不同的值，在这两个位置中存在的设置，需要选择如何继续。 请确保你已保存的更改到 local.settings.json 文件运行此命令之前。 |
| **在 GitHub 中查看提交** | 显示的最新提交中特定部署函数应用已连接到存储库时。 |
| **查看部署日志** | 显示您到函数应用的特定部署的日志在 Azure 中。 |

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Functions 核心工具，请参阅[在本地编写 Azure 函数代码并对其进行测试](functions-run-local.md)。

若要了解有关以 .NET 类库开发函数的详细信息，请参阅 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)。 本文还举例说明了如何使用属性来声明 Azure Functions 支持的各种类型的绑定。    

[适用于 Visual Studio Code 的 Azure Functions 扩展]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
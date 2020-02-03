---
title: 使用 Visual Studio Code 开发 Azure Functions
description: 了解如何使用适用于 Visual Studio Code 的 Azure Functions 扩展来开发和测试 Azure Functions。
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 7e533d5826d429a716ad1592d75159782ed43fa7
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964001"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>使用 Visual Studio Code 开发 Azure Functions

[适用于 Visual Studio Code 的 Azure Functions 扩展]使你能够在本地开发功能并将其部署到 Azure。 如果这是你第一次体验 Azure Functions，可以在 [Azure Functions 简介](functions-overview.md)中了解详细信息。

Azure Functions 扩展提供以下好处：

* 在本地开发计算机上编辑、生成和运行函数。
* 将 Azure Functions 项目直接发布到 Azure。
* 使用各种语言编写函数，同时利用 Visual Studio Code 的好处。

该扩展可与以下语言配合使用：从版本2.x 开始 Azure Functions 运行时支持：

* [C#编译](functions-dotnet-class-library.md)
* <sup></sup> [ C#脚本](functions-reference-csharp.md)*
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>要求将 "[脚本C# " 设置为默认的项目语言](#c-script-projects)。

在本文中，示例当前仅适用于 JavaScript （node.js）和C#类库函数。  

本文详细介绍了如何使用 Azure Functions 扩展来开发功能并将其发布到 Azure。 阅读本文之前，应[使用 Visual Studio Code 创建第一个函数](functions-create-first-function-vs-code.md)。

> [!IMPORTANT]
> 不要为单一函数应用混合本地开发和门户开发。 从本地项目发布到函数应用时，部署过程会覆盖在门户中开发的任何函数。

## <a name="prerequisites"></a>必备组件

在 Visual Studio Code [适用于 visual studio code 的 azure functions 扩展][适用于 visual studio code 的 azure functions 扩展]之前，必须满足以下要求：

* [Visual Studio Code](https://code.visualstudio.com/)安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上。

* 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

当你[使用 Visual Studio Code 发布](#publish-to-azure)时，你需要的其他资源（例如 Azure 存储帐户）在你的订阅中创建。

> [!IMPORTANT]
> 你可以在本地开发功能并将其发布到 Azure，而无需在本地启动并运行它们。 若要在本地运行函数，需要满足一些其他要求，包括自动下载 Azure Functions Core Tools。 若要了解详细信息，请参阅在[本地运行项目的其他要求](#additional-requirements-for-running-a-project-locally)。

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>创建 Azure Functions 项目

函数扩展使你能够创建函数应用项目以及第一个函数。 以下步骤说明如何在新的函数项目中创建 HTTP 触发的函数。 [HTTP 触发器](functions-bindings-http-webhook.md)是用于演示的最简单的函数触发器模板。

1. 从 " **Azure：函数**" 中，选择 "**创建函数**" 图标：

    ![创建函数](./media/functions-develop-vs-code/create-function.png)

1. 选择 function app 项目的文件夹，然后为**函数项目选择语言**。

1. 如果尚未安装核心工具，系统会要求选择要安装的核心工具**版本**。 选择版本2.x 或更高版本。 

1. 选择 " **HTTP 触发器**函数" 模板，也可以选择 "**跳过**" 以创建没有函数的项目。 你随时可以在[项目中添加函数](#add-a-function-to-your-project)。

    ![选择 HTTP 触发器模板](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 为 "函数名称" 键入**HttpExample** ，然后选择 Enter，然后选择 "**函数**授权"。 此授权级别要求你在调用函数终结点时提供[功能键](functions-bindings-http-webhook.md#authorization-keys)。

    ![选择函数授权](./media/functions-develop-vs-code/create-function-auth.png)

    函数是在所选语言和 HTTP 触发的函数的模板中创建的。

    ![Visual Studio Code 中的 HTTP 触发函数模板](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>生成的项目文件

项目模板用所选的语言创建项目并安装所需的依赖项。 对于任何语言，新项目包含以下文件：

* **host.json**：用于配置 Functions 主机。 当你在本地运行函数以及在 Azure 中运行它们时，将应用这些设置。 有关详细信息，请参阅 [host.json 参考](functions-host-json.md)。

* **local. json**：维护在本地运行函数时使用的设置。 仅当在本地运行函数时，才使用这些设置。 有关详细信息，请参阅[本地设置文件](#local-settings-file)。

    >[!IMPORTANT]
    >由于本地. json 文件可能包含机密，因此需要从项目源代码管理中排除它。

根据你的语言，将创建这些其他文件：

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

* 实现函数的[HttpExample.cs 类库文件](functions-dotnet-class-library.md#functions-class-library-project)。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

* 根文件夹中的包文件。

* 一个 HttpExample 文件夹，其中包含[函数 json 定义文件](functions-reference-node.md#folder-structure)和一个 node.js[文件](functions-reference-node.md#exporting-a-function)，其中包含函数代码。

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

此时，可以通过[修改函数 json 文件](#add-a-function-to-your-project)或通过[将参数添加到C#类库函数](#add-a-function-to-your-project)来向函数添加输入和输出绑定。

还可以向[项目中添加新函数](#add-a-function-to-your-project)。

## <a name="install-binding-extensions"></a>安装绑定扩展

除 HTTP 和 timer 触发器外，绑定在扩展包中实现。 必须为需要的触发器和绑定安装扩展包。 安装绑定扩展的过程取决于项目的语言。

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

在终端窗口中运行[dotnet add package](/dotnet/core/tools/dotnet-add-package)命令，以在项目中安装所需的扩展包。 以下命令将安装 Azure 存储扩展，该扩展可实现 Blob、队列和表存储的绑定。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>将函数添加到项目

您可以通过使用预定义的函数触发器模板之一向现有项目中添加新函数。 若要添加新的函数触发器，请选择 F1 打开命令面板，然后搜索并运行命令**Azure Functions： Create function**。 按照提示选择触发器类型并定义触发器的必需属性。 如果触发器需要访问密钥或连接字符串才能连接到服务，请在创建函数触发器之前准备好该触发器。

此操作的结果取决于项目的语言：

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

新C#的类库（.cs）文件将添加到你的项目中。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

在项目中创建一个新文件夹。 文件夹包含新的函数 json 文件和新的 JavaScript 代码文件。

---

## <a name="add-input-and-output-bindings"></a>添加输入和输出绑定

可以通过添加输入和输出绑定来扩展函数。 添加绑定的过程取决于项目的语言。 若要了解有关绑定的详细信息，请参阅[Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

以下示例连接到名为 `outqueue`的存储队列，在该队列中，存储帐户的连接字符串是在 `MyStorageConnection` 应用程序设置中设置的。

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

更新函数方法，将以下参数添加到 `Run` 方法定义：

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

此代码要求你添加以下 `using` 语句：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` 参数为 `ICollector<T>` 类型，表示函数完成时写入输出绑定的消息集合。 向集合中添加一条或多条消息。 当函数完成时，这些消息将发送到队列。

若要了解详细信息，请参阅[队列存储输出绑定](functions-bindings-storage-queue.md#output)文档。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Visual Studio Code 使你可以通过使用一组方便的提示向函数 json 文件添加绑定。 若要创建绑定，请右键单击（Ctrl + 单击 macOS）函数文件夹中的**函数 json**文件，然后选择 "**添加绑定**"：

![将绑定添加到现有 JavaScript 函数 ](media/functions-develop-vs-code/function-add-binding.png)

下面是用于定义新的存储输出绑定的示例提示：

| Prompt | 值 | Description |
| -------- | ----- | ----------- |
| **选择绑定方向** | `out` | 该绑定是输出绑定。 |
| **选择方向绑定** | `Azure Queue Storage` | 该绑定是 Azure 存储队列绑定。 |
| **用于在代码中标识此绑定的名称** | `msg` | 用于标识代码中引用的绑定参数的名称。 |
| **要将消息发送到的队列** | `outqueue` | 绑定要写入到的队列的名称。 如果 *queueName* 不存在，首次使用绑定时，它会创建该属性。 |
| **从 "local. json" 中选择设置** | `MyStorageConnection` | 包含存储帐户连接字符串的应用程序设置的名称。 `AzureWebJobsStorage` 设置包含用 function app 创建的存储帐户的连接字符串。 |

在此示例中，将以下绑定添加到函数 json 文件中的 `bindings` 数组：

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

还可以将相同的绑定定义直接添加到函数 json。

在函数代码中，从 `context`访问 `msg` 绑定，如以下示例中所示：

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

若要了解详细信息，请参阅[队列存储输出绑定](functions-bindings-storage-queue.md#output)引用。

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>发布到 Azure

Visual Studio Code 允许你将函数项目直接发布到 Azure。 在此过程中，将在 Azure 订阅中创建函数应用和相关的资源。 函数应用为函数提供了执行上下文。 该项目将打包并部署到 Azure 订阅中的新函数应用。

当你从 Visual Studio Code 发布到 Azure 中的新函数应用时，将为你提供快速函数应用创建路径和高级路径。 

从 Visual Studio Code 发布时，可以利用[Zip 部署](functions-deployment-technologies.md#zip-deploy)技术。 

### <a name="quick-function-app-create"></a>快速函数应用创建

当你**在 Azure 中选择 "+ 新建函数应用 ...** " 时，扩展会自动为函数应用所需的 Azure 资源生成值。 这些值基于你选择的函数应用名称。 有关使用默认值将项目发布到 Azure 中的新函数应用的示例，请参阅[Visual Studio Code 快速入门文章](functions-create-first-function-vs-code.md#publish-the-project-to-azure)。

如果要为创建的资源提供显式名称，则必须选择 "高级" 创建路径。

### <a name="enable-publishing-with-advanced-create-options"></a>使用高级选项将项目发布到 Azure 中的新函数应用

以下步骤将项目发布到使用高级 create 选项创建的新函数应用：

1. 在 " **Azure：函数**" 区域中，选择 "**部署到 Function App** " 图标。

    ![函数应用设置](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 如果你尚未登录，系统将提示你**登录到 Azure**。 还可以**创建免费 Azure 帐户**。 从浏览器登录后，返回到 Visual Studio Code。

1. 如果有多个订阅，请选择 function app 的**订阅**，然后选择 " **+ 在 Azure 中创建新 Function App ..."_Advanced_** 。 此_高级_选项使你可以更好地控制在 Azure 中创建的资源。 

1. 按照提示操作，提供以下信息：

    | Prompt | 值 | Description |
    | ------ | ----- | ----------- |
    | 在 Azure 中选择 function app | 在 Azure 中创建新 Function App | 在下一个提示符下，键入标识新函数应用的全局唯一名称，然后选择 "Enter"。 函数应用名称的有效字符包括 `a-z`、`0-9` 和 `-`。 |
    | 选择 OS | Windows | 函数应用在 Windows 上运行。 |
    | 选择托管计划 | 消耗计划 | 使用无服务器[消耗计划托管](functions-scale.md#consumption-plan)。 |
    | 为新应用选择运行时 | 你的项目语言 | 运行时必须与要发布的项目匹配。 |
    | 为新资源选择资源组 | 创建新的资源组 | 在下一个提示符下，键入资源组名称（如 `myResourceGroup`），然后选择 enter。 还可以选择现有的资源组。 |
    | 选择存储帐户 | 新建存储帐户 | 在下一个提示符下，键入函数应用使用的新存储帐户的全局唯一名称，然后选择 "Enter"。 存储帐户名称的长度必须介于3到24个字符之间，并且只能包含数字和小写字母。 你还可以选择一个现有帐户。 |
    | 选择新资源的位置 | region | 选择靠近你或你的函数访问的其他服务附近的[区域](https://azure.microsoft.com/regions/)中的位置。 |

    创建函数应用并应用部署包后，将显示一条通知。 在此通知中选择“查看输出”以查看创建和部署结果，其中包括你创建的 Azure 资源。

## <a name="republish-project-files"></a>重新发布项目文件

设置[连续部署](functions-continuous-deployment.md)时，只要在连接的源位置中更新了源文件，就会更新 Azure 中的函数应用。 建议持续部署，但也可以从 Visual Studio Code 重新发布项目文件更新。

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。

1. 在 Visual Studio Code 中，选择 F1 打开命令面板。 在命令面板中，搜索并选择 " **Azure Functions：部署到函数应用**"。

1. 如果你尚未登录，系统将提示你**登录到 Azure**。 从浏览器登录后，返回到 Visual Studio Code。 如果有多个订阅，请选择包含 function app 的**订阅**。

1. 在 Azure 中选择现有的函数应用。 警告您覆盖函数应用中的所有文件时，选择 "**部署**" 以确认警告并继续。

重新生成项目并将其重新打包并上传到 Azure。 现有项目将被新包替换，并且函数应用将重新启动。

## <a name="get-the-url-of-the-deployed-function"></a>获取已部署函数的 URL

若要调用 HTTP 触发的函数，需要将函数的 URL 部署到 function app。 此 URL 包括任何所需的[功能键](functions-bindings-http-webhook.md#authorization-keys)。 您可以使用此扩展获取已部署函数的这些 Url。

1. 选择 F1 打开命令面板，然后搜索并运行命令**Azure Functions：复制函数 URL**。

1. 按照提示选择 Azure 中的函数应用，然后选择要调用的特定 HTTP 触发器。

函数 URL 将连同 `code` 查询参数传递的任何必需密钥一起复制到剪贴板。 使用 HTTP 工具提交 POST 请求，或使用浏览器将 GET 请求发送到远程函数。  

## <a name="run-functions-locally"></a>在本地运行函数

Azure Functions 扩展使你能够在本地开发计算机上运行函数项目。 本地运行时是在 Azure 中托管 function app 的相同运行时。 本地设置是从 web.config[文件](#local-settings-file)中读取的。

### <a name="additional-requirements-for-running-a-project-locally"></a>在本地运行项目的其他要求

若要在本地运行函数项目，必须满足以下附加要求：

* 安装[Azure Functions Core Tools](functions-run-local.md#v2)的版本4.x 或更高版本。 当你在本地启动项目时，将自动下载并安装 Core 工具包。 核心工具包括整个 Azure Functions 运行时，因此下载和安装可能需要一段时间。

* 针对所选语言安装特定必需组件：

    | 语言 | 要求 |
    | -------- | --------- |
    | **C#** | [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Java 扩展调试器](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 或更高版本](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>建议使用[Python 3.6.8](https://www.python.org/downloads/)|

    <sup>*</sup>活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。

### <a name="configure-the-project-to-run-locally"></a>将项目配置为在本地运行

函数运行时对 HTTP 和 webhook 以外的所有触发器类型使用一个 Azure 存储帐户。 因此，需要将**AzureWebJobsStorage**密钥设置为有效的 Azure 存储帐户连接字符串。

本部分使用[适用于 Visual Studio Code 的 Azure 存储扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)， [Azure 存储资源管理器](https://storageexplorer.com/)连接到存储连接字符串并进行检索。

若要设置存储帐户连接字符串，请执行以下操作：

1. 在 Visual Studio 中，打开**Cloud Explorer**，**展开存储帐户 > 存储帐户** **，然后**选择 "**属性**" 并复制 "**主连接字符串**" 值。

2. 在项目内，打开 local.settings.json 项目文件，并将“AzureWebJobsStorage”键的值设置为复制的连接字符串。

3. 重复上述步骤，将唯一键添加到函数所需的其他任何连接的 **Values** 数组。

有关详细信息，请参阅[本地设置文件](#local-settings-file)。

### <a name="debugging-functions-locally"></a>本地调试函数  

若要调试函数，请选择 F5。 如果尚未下载[核心工具][Azure Functions Core Tools]，系统将提示你执行此操作。 当安装并运行核心工具时，输出将显示在终端中。 这与从终端运行 `func host start` Core Tools 命令相同，但使用其他生成任务和附加调试器。  

项目运行时，可以像将项目部署到 Azure 时一样触发函数。 在调试模式下运行项目时，断点会按预期命中 Visual Studio Code 中。

HTTP 触发器的请求 URL 显示在终端的输出中。 当项目在本地运行时，不使用 HTTP 触发器的功能键。 有关详细信息，请参阅[在 Azure Functions 中测试代码的策略](functions-test-a-function.md)。  

若要了解详细信息，请参阅[使用 Azure Functions Core Tools][Azure Functions Core Tools]。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

默认情况下，将项目发布到 Azure 时，不会自动迁移这些设置。 发布完成后，可以选择将本地. json 中的设置发布到 Azure 中的函数应用。 若要了解详细信息，请参阅[发布应用程序设置](#publish-application-settings)。

**ConnectionStrings** 中的值永远不会发布。

函数应用程序设置值还可以作为环境变量在代码中读取。 有关详细信息，请参阅以下特定于语言的参考文章中的环境变量部分：

* [预编译 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 脚本 (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure 中的应用程序设置

项目中的 web.config 文件中的设置应与 Azure 中函数应用中的应用程序设置相同。 你添加到本地. json 的任何设置还必须添加到 Azure 中的函数应用。 发布项目时，不会自动上传这些设置。 同样，在[门户](functions-how-to-use-azure-function-app-settings.md#settings)的函数应用中创建的任何设置都必须下载到本地项目中。

### <a name="publish-application-settings"></a>发布应用程序设置

若要将所需的设置发布到 Azure 中的函数应用，最简单的方法是使用发布项目后显示的 "**上传设置**" 链接：

![上传应用程序设置](./media/functions-develop-vs-code/upload-app-settings.png)

你还可以使用 "命令" 面板中的 " **Azure Functions：上传本地设置**" 命令来发布设置。 你可以使用**Azure Functions：添加新设置**命令，将单个设置添加到 Azure 中的应用程序设置。

> [!TIP]
> 在发布之前，请务必保存您的 web.config 文件。

如果对本地文件进行了加密，则将对其进行解密、发布并再次加密。 如果在这两个位置有冲突的设置，则系统会提示您选择如何继续。

通过展开你的订阅、函数应用和**应用程序设置**，在**Azure： "功能**" 区域中查看现有的应用设置。

![在 Visual Studio Code 中查看函数应用设置](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>从 Azure 下载设置

如果已在 Azure 中创建应用程序设置，则可以使用**Azure Functions：下载远程设置**命令将它们下载到你的本地. json 文件中。

与上传一样，如果对本地文件进行了加密，则将对其进行解密、更新和再次加密。 如果在这两个位置有冲突的设置，则系统会提示您选择如何继续。

## <a name="monitoring-functions"></a>监视函数

在[本地运行函数](#run-functions-locally)时，会将日志数据流式传输到终端控制台。 当函数项目在 Azure 的函数应用中运行时，还可以获取日志数据。 可以连接到 Azure 中的流式传输日志来查看近乎实时的日志数据，也可以启用 Application Insights 以便更全面地了解 function app 的行为方式。

### <a name="streaming-logs"></a>流式处理日志

当你开发应用程序时，通常以近乎实时的时间查看日志记录信息通常很有用。 您可以查看函数正在生成的日志文件流。 此输出是对 HTTP 触发的函数发出请求的流式处理日志的示例：

![HTTP 触发器的流式传输日志输出](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

若要了解详细信息，请参阅[流式处理日志](functions-monitoring.md#streaming-logs)。

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 流式处理日志仅支持函数主机的单个实例。 将函数扩展到多个实例时，不会在日志流中显示其他实例的数据。 Application Insights 中的[实时指标流](../azure-monitor/app/live-stream.md)支持多个实例。 同时，在近乎实时的情况下，流式分析基于[抽样数据](functions-monitoring.md#configure-sampling)。

### <a name="application-insights"></a>Application Insights

建议通过将 function app 与 Application Insights 集成来监视函数的执行。 在 Azure 门户中创建函数应用时，默认情况下会发生此集成。 在 Visual Studio 发布过程中创建函数应用时，需要自行集成 Application Insights。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

## <a name="c-script-projects"></a>C\# 脚本项目

默认情况下， C#所有项目都作为[ C#编译的类库项目](functions-dotnet-class-library.md)创建。 如果希望改为使用C#脚本项目，则必须在 Azure Functions 扩展C#设置中选择 "脚本" 作为默认语言：

1. 选择 "**文件** > **首选项** > **设置**"。

1.  > **扩展** > **Azure Functions**中转到 "**用户设置**"。

1. 从**Azure Function：项目语言**中选择**C # 脚本**。

完成这些步骤后，对基础核心工具的调用包括 `--csx` 选项，该选项将生成和发布C#脚本（run.csx）项目文件。 如果您指定了此默认语言，则您创建的所有项目都C#默认为脚本项目。 设置默认值时，系统不会提示您选择项目语言。 若要用其他语言创建项目，必须更改此设置或将其从用户设置 json 文件中删除。 删除此设置后，在您创建项目时，系统会再次提示您选择所用的语言。

## <a name="command-palette-reference"></a>命令面板参考

Azure Functions 扩展在区域中提供了一个有用的图形界面，用于与 Azure 中的函数应用交互。 命令面板（F1）中也提供了相同的功能。 以下 Azure Functions 命令可用：

|Azure Functions 命令  | Description  |
|---------|---------|
|**添加新设置**  |  在 Azure 中创建新的应用程序设置。 若要了解详细信息，请参阅[发布应用程序设置](#publish-application-settings)。 你可能还需要将[此设置下载到本地设置](#download-settings-from-azure)。 |
| **配置部署源** | 将 Azure 中的函数应用连接到本地 Git 存储库。 若要了解详细信息，请参阅[Azure Functions 的持续部署](functions-continuous-deployment.md)。 |
| **连接到 GitHub 存储库** | 将函数应用连接到 GitHub 存储库。 |
| **复制函数 URL** | 获取在 Azure 中运行的 HTTP 触发的函数的远程 URL。 若要了解详细信息，请参阅[获取已部署函数的 URL](#get-the-url-of-the-deployed-function)。 |
| **在 Azure 中创建 function app** | 在 Azure 订阅中创建新的 function app。 若要了解详细信息，请参阅有关如何[发布到 Azure 中的新函数应用](#publish-to-azure)的部分。        |
| **解密设置** | 对已由**Azure Functions：加密设置**加密的[本地设置](#local-settings-file)进行解密。  |
| **删除 Function App** | 从 Azure 中的订阅删除函数应用。 当应用服务计划中没有其他应用时，也会提供删除此项的选项。 其他资源（如存储帐户和资源组）不会被删除。 若要删除所有资源，应改为[删除资源组](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)。 你的本地项目不受影响。 |
|**删除函数**  | 从 Azure 中的函数应用删除现有函数。 由于此删除操作不会影响本地项目，因此请考虑在本地删除该函数，然后重新[发布项目](#republish-project-files)。 |
| **删除代理** | 从 Azure 中的函数应用删除 Azure Functions 代理。 若要了解有关代理的详细信息，请参阅[使用 Azure Functions 代理](functions-proxies.md)。 |
| **删除设置** | 删除 Azure 中的 function app 设置。 此删除操作不会影响你的 web.config 文件中的设置。 |
| **从存储库断开连接**  | 删除 Azure 中的函数应用与源代码管理存储库之间的[连续部署](functions-continuous-deployment.md)连接。 |
| **下载远程设置** | 将 Azure 中选择的函数应用的设置下载到你的本地设置文件中。 如果对本地文件进行了加密，则将对其进行解密、更新和再次加密。 如果在这两个位置有冲突的设置，则系统会提示您选择如何继续。 在运行此命令之前，请确保将更改保存到你的本地设置文件中。 |
| **编辑设置** | 更改 Azure 中现有 function app 设置的值。 此命令不会影响你的 web.config 文件中的设置。  |
| **加密设置** | 在[本地设置](#local-settings-file)中加密 `Values` 数组中的单个项。 在此文件中，还将 `IsEncrypted` 设置为 `true`，该值指定本地运行时将在使用这些设置之前对其进行解密。 加密本地设置以降低泄露重要信息的风险。 在 Azure 中，应用程序设置始终以加密的存储。 |
| **立即执行函数** | 在 Azure 中手动启动[计时器触发的函数](functions-bindings-timer.md)。 此命令用于测试。 若要了解有关在 Azure 中触发非 HTTP 函数的详细信息，请参阅[手动运行非 http 触发的函数](functions-manually-run-non-http.md)。 |
| **初始化项目以用于 VS Code** | 将所需的 Visual Studio Code 项目文件添加到现有函数项目。 使用此命令可以处理使用核心工具创建的项目。 |
| **安装或更新 Azure Functions Core Tools** | [Azure Functions Core Tools]安装或更新，用于本地运行函数。 |
| **部署**  | 允许将项目文件从连接的 Git 存储库重新部署到 Azure 中的特定部署。 若要重新发布 Visual Studio Code 的本地更新，请重新[发布项目](#republish-project-files)。 |
| **重命名设置** | 更改 Azure 中现有 function app 设置的密钥名称。 此命令不会影响你的 web.config 文件中的设置。 重命名 Azure 中的设置后，应将[这些更改下载到本地项目](#download-settings-from-azure)中。 |
| **重启** | 在 Azure 中重新启动函数应用。 部署更新还会重新启动函数应用。 |
| **设置 AzureWebJobsStorage**| 设置 `AzureWebJobsStorage` 应用程序设置的值。 Azure Functions 需要此设置。 它在 Azure 中创建函数应用时进行设置。 |
| **启动** | 在 Azure 中启动已停止的函数应用。 |
| **启动流式处理日志** | 在 Azure 中启动函数应用的流式处理日志。 如果需要近乎实时地查看日志记录信息，请在 Azure 中进行远程故障排除时使用流式处理日志。 若要了解详细信息，请参阅[流式处理日志](#streaming-logs)。 |
| **Stop** | 停止在 Azure 中运行的 function app。 |
| **停止流式处理日志** | 停止 Azure 中的函数应用的流式处理日志。 |
| **切换为槽设置** | 启用后，确保应用程序设置对于给定的部署槽保持不变。 |
| **卸载 Azure Functions Core Tools** | 删除扩展所需的 Azure Functions Core Tools。 |
| **上载本地设置** | 将本地. json 文件中的设置上载到 Azure 中选择的函数应用。 如果对本地文件进行加密，将对其进行解密、上传和加密。 如果在这两个位置有冲突的设置，则系统会提示您选择如何继续。 在运行此命令之前，请确保将更改保存到你的本地设置文件中。 |
| **在 GitHub 中查看提交** | 当函数应用连接到存储库时，将显示特定部署中的最新提交。 |
| **查看部署日志** | 显示用于特定部署到 Azure 中的函数应用的日志。 |

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Functions Core Tools 的详细信息，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。

若要了解有关以 .NET 类库开发函数的详细信息，请参阅 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)。 本文还提供了一些示例链接，这些示例介绍了如何使用属性来声明 Azure Functions 支持的各种类型的绑定。

[适用于 Visual Studio Code 的 Azure Functions 扩展]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md

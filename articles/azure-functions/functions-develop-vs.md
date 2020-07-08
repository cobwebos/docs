---
title: 使用 Visual Studio 开发 Azure Functions
description: 了解如何使用用于 Visual Studio 2019 的 Azure Functions 工具开发和测试 Azure Functions。
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 0df65c4cdbf40b49bc265f4accafde67f41dafb7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730973"
---
# <a name="develop-azure-functions-using-visual-studio"></a>使用 Visual Studio 开发 Azure Functions  

使用 Visual Studio 可以开发、测试 C# 类库函数并将其部署到 Azure。 如果这是你第一次使用 Azure Functions，请参阅[Azure Functions 简介](functions-overview.md)。

开发函数时，Visual Studio 提供了以下优势： 

* 在本地开发计算机上编辑、生成和运行函数。 
* 将 Azure Functions 项目直接发布到 Azure，并根据需要创建 Azure 资源。 
* 使用 C# 特性直接在 C# 代码中声明函数绑定。
* 开发和部署预先编译的 C# 函数。 与基于 C# 脚本的函数相比，预先编译的函数的冷启动性能更好。 
* 可以在 C# 中编写函数的代码，同时利用 Visual Studio 开发环境的所有优势。 

本文详细介绍如何使用 Visual Studio 开发 C# 类库函数并将其发布到 Azure。 阅读本文之前，请考虑完成[Visual Studio 的函数快速入门](functions-create-your-first-function-visual-studio.md)。 

除非另有说明，否则将演示 Visual Studio 2019 的过程和示例。 

## <a name="prerequisites"></a>必备条件

- Azure Functions 工具。 若要添加 Azure Function Tools，请在 Visual Studio 安装中包括**azure 开发**工作负荷。 从 Visual Studio 2017 开始，Azure 开发工作负荷中提供了 Azure Functions 工具。

- 所需的其他资源（例如 Azure 存储帐户）将在发布过程中在订阅中创建。

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 在 Visual Studio 2017 中，Azure 开发工作负荷将 Azure Functions 工具安装为单独的扩展。 更新 Visual Studio 2017 安装时，请确保使用的是[最新版本](#check-your-tools-version)的 Azure Functions 工具。 以下部分介绍如何在 Visual Studio 2017 中检查和更新（如果需要）Azure Functions Tools 扩展。 
>
> 如果使用的是 Visual Studio 2019，请跳过这些部分。

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>在 Visual Studio 2017 中检查工具版本

1. 在“工具”菜单中，选择“扩展和更新”   。 展开 "**已安装**  >  的**工具**"，然后选择 " **Azure Functions 和 Web 作业工具**"。

    ![验证 Functions 工具版本](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. 记下已安装的**版本**，并将此版本与[发行说明](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)中列出的最新版本进行比较。 

1. 如果你的版本较旧，可以在 Visual Studio 中更新工具，如以下部分中所示。

### <a name="update-your-tools-in-visual-studio-2017"></a>在 Visual Studio 2017 中更新工具

1. 在“扩展和更新”**** 对话框中，展开“更新”**** > “Visual Studio Marketplace”****，依次选择“Azure Functions 和 Web 作业工具”**** 和“更新”****。

    ![更新 Functions 工具版本](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. 下载工具更新后，选择 "**关闭**"，然后关闭 Visual Studio 以触发带有 VSIX 安装程序的工具更新。

1. 在 VSIX 安装程序中，选择 "**修改**" 以更新工具。 

1. 更新完成后，选择 "**关闭**"，然后重启 Visual Studio。

> [!NOTE]  
> 在 Visual Studio 2019 和更高版本中，Azure Functions Tools 扩展将作为 Visual Studio 的一部分更新。  

## <a name="create-an-azure-functions-project"></a>创建 Azure Functions 项目

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

创建 Azure Functions 项目后，项目模板将创建一个 c # 项目，安装 `Microsoft.NET.Sdk.Functions` NuGet 包，并设置目标框架。 新项目包含以下文件：

* **host.json**：用于配置 Functions 主机。 在本地和 Azure 中运行时，都会应用这些设置。 有关详细信息，请参阅 [host.json 参考](functions-host-json.md)。

* **local.settings.json**：维护本地运行函数时使用的设置。 在 Azure 中运行时不使用这些设置。 有关详细信息，请参阅[本地设置文件](#local-settings-file)。

    >[!IMPORTANT]
    >由于文件上的 local.settings.js可以包含机密，因此你必须从项目源代码管理中排除它。 请确保将此文件的 "**复制到输出目录**" 设置设为 "**如果较新则复制**"。 

有关详细信息，请参阅 [Functions 类库项目](functions-dotnet-class-library.md#functions-class-library-project)。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

发布项目时，Visual Studio 不会自动上传 local.settings.js上的设置。 若要确保这些设置也存在于 Azure 的函数应用中，请在发布项目后上传这些设置。 有关详细信息，请参阅[Function app settings](#function-app-settings)。 集合中的值 `ConnectionStrings` 永远不会发布。

你的代码还可以读取函数应用设置值作为环境变量。 有关详细信息，请参阅[环境变量](functions-dotnet-class-library.md#environment-variables)。

## <a name="configure-the-project-for-local-development"></a>为本地开发配置项目

Functions 运行时在内部使用 Azure 存储帐户。 对于除 HTTP 和 webhook 以外的所有触发器类型，请将 `Values.AzureWebJobsStorage` 密钥设置为有效的 Azure 存储帐户连接字符串。 函数应用还可以使用[Azure 存储模拟器](../storage/common/storage-use-emulator.md)来实现 `AzureWebJobsStorage` 项目所需的连接设置。 若要使用仿真程序，请将的值设置 `AzureWebJobsStorage` 为 `UseDevelopmentStorage=true` 。 在部署之前，请将此设置更改为实际的存储帐户连接字符串。

若要设置存储帐户连接字符串，请执行以下操作：

1. 在 Visual Studio 中，选择 "**查看**  >  **Cloud Explorer**"。

2. 在**Cloud Explorer**中，展开 "**存储帐户**"，然后选择存储帐户。 在 "**属性**" 选项卡中，复制 "**主连接字符串**" 值。

2. 在项目中，打开文件的 local.settings.js，并将项的值设置 `AzureWebJobsStorage` 为所复制的连接字符串。

3. 重复上述步骤，将唯一键添加到 `Values` 数组中函数所需的任何其他连接。 

## <a name="add-a-function-to-your-project"></a>将函数添加到项目

在 C# 类库函数中，可以通过在代码中应用属性来定义函数使用的绑定。 从提供的模板创建函数触发器时，将为你应用触发器属性。 

1. 在**解决方案资源管理器**中，右键单击项目节点，然后选择 "**添加**  >  **新项**"。 

2. 选择 " **Azure 函数**"，输入类的**名称**，然后选择 "**添加**"。

3. 选择触发器，设置绑定属性，然后选择 **"确定"**。 下面的示例演示用于创建队列存储触发器函数的设置。 

    ![创建队列存储触发器函数](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    此触发器示例使用具有名为的密钥的连接字符串 `QueueStorage` 。 在[local.settings.js文件上](functions-run-local.md#local-settings-file)定义此连接字符串设置。

4. 检查新添加的类。 你会看到一个 `Run()` 使用特性特性化的静态方法 `FunctionName` 。 该属性指示该方法是函数的入口点。

    例如，下面的 c # 类表示一个基本的队列存储触发器函数：

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

已向提供给入口点方法的每个绑定参数提供了特定于绑定的属性。 该属性采用绑定信息作为参数。 在上面的示例中，第一个参数 `QueueTrigger` 应用了属性，指示队列存储触发器函数。 队列名称和连接字符串设置名称作为参数传递到 `QueueTrigger` 属性。 有关详细信息，请参阅 [Azure Functions 的 Azure 队列存储绑定](functions-bindings-storage-queue-trigger.md)。

使用上述过程向函数应用项目添加更多函数。 项目中的每个函数可以有不同的触发器，但每个函数的触发器必须刚好一个。 有关详细信息，请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

## <a name="add-bindings"></a>添加绑定

使用触发器时，输入和输出绑定是作为绑定属性添加到函数的。 向函数添加绑定，如下所示：

1. 确保已[为本地开发配置项目](#configure-the-project-for-local-development)。

2. 为特定绑定添加适当的 NuGet 扩展包。 

   有关详细信息，请参阅[带有 Visual Studio 的 c # 类库](./functions-bindings-register.md#local-csharp)。 在绑定的参考文章中找到特定于绑定的 NuGet 包要求。 例如，可以在[事件中心绑定参考文章](functions-bindings-event-hubs.md)中找到事件中心触发器的包要求。

3. 如果存在绑定需要的应用设置，请将它们添加到 `Values` [本地设置文件](functions-run-local.md#local-settings-file)中的集合。 

   函数在本地运行时使用这些值。 当函数在 Azure 中的函数应用中运行时，它将使用[function app 设置](#function-app-settings)。

4. 将适当的绑定属性添加到方法签名。 在以下示例中，一条队列消息触发了该函数，而输出绑定则创建了一条新的队列消息，在不同的队列中使用了相同的文本。

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   到队列存储的连接从 `AzureWebJobsStorage` 设置获取。 有关详细信息，请参阅特定绑定的参考文章。 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>测试函数

Azure Functions Core Tools 允许在本地开发计算机上运行 Azure Functions 项目。 有关详细信息，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。 第一次从 Visual Studio 启动函数时，系统将提示您安装这些工具。 

在 Visual Studio 中测试函数：

1. 按 F5。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具。 你还需要启用防火墙例外，这样工具才能处理 HTTP 请求。

2. 运行项目时，请测试代码，就像测试已部署的函数一样。 

   有关详细信息，请参阅[在 Azure Functions 中测试代码的策略](functions-test-a-function.md)。 在调试模式下运行 Visual Studio 时，断点按预期方式命中。

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>发布到 Azure

从 Visual Studio 发布时，它使用以下两种部署方法之一：

* [Web 部署](functions-deployment-technologies.md#web-deploy-msdeploy)：打包 Windows 应用并将其部署到任何 IIS 服务器。
* [已启用从包运行的 Zip 部署](functions-deployment-technologies.md#zip-deploy)：建议用于 Azure Functions 部署。

使用以下步骤将项目发布到 Azure 中的函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>函数应用设置

由于 Visual Studio 不会在你发布项目时自动上传这些设置，因此你在中的 local.settings.js添加的任何设置还必须添加到 Azure 中的函数应用。

若要将所需的设置上载到 Azure 中的函数应用，最简单的方法是选择 "**管理 Azure App Service 设置**" 链接，该链接将在成功发布项目后显示。

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="发布窗口中的设置":::

选择此链接将显示函数应用的 "**应用程序设置**" 对话框，你可以在其中添加新的应用程序设置或修改现有的应用程序设置。

![应用程序设置](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**本地**在 local.settings.js的文件中显示设置值，而**Remote**则在 Azure 的函数应用中显示当前设置值。 选择“添加设置”**** 以创建新的应用设置。 使用“从本地插入值”**** 链接将设置值复制到“远程”**** 字段。 你选择“确定”**** 后，挂起的更改将写入本地设置文件和函数应用。

> [!NOTE]
> 默认情况下，不会将 local.settings.json 文件签入到源代码管理中。 这意味着，如果克隆源代码管理中的本地函数项目，该项目没有 local.settings.js的文件。 在这种情况下，需要在项目根目录中手动创建 local.settings.js文件，使**应用程序设置**对话框按预期方式工作。 

还可以采用以下这些其他方法之一来管理应用程序设置：

* [使用 Azure 门户](functions-how-to-use-azure-function-app-settings.md#settings)。
* [使用 `--publish-local-settings` Azure Functions Core Tools 中的 "发布" 选项](functions-run-local.md#publish)。
* [使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)。

## <a name="monitoring-functions"></a>监视函数

监视函数执行的建议方法是将函数应用与 Azure Application Insights 集成。 在 Azure 门户中创建函数应用时，默认情况下会为你完成此集成。 但是，在 Visual Studio 发布期间创建函数应用时，Azure 中的函数应用集成未完成。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Core Tools 的详细信息，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。

有关将函数作为 .NET 类库进行开发的详细信息，请参阅[Azure Functions c # 开发人员参考](functions-dotnet-class-library.md)。 本文还举例说明了如何使用属性来声明 Azure Functions 支持的各种类型的绑定。    

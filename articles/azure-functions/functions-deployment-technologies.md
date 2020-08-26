---
title: Azure Functions 中的部署技术
description: 了解将代码部署到 Azure Functions 的不同方式。
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 3865e6906b39633e14c86619770188f1c73fed8e
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641953"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions 中的部署技术

可以使用多种不同的技术将 Azure Functions 项目代码部署到 Azure。 本文概述了可用的部署方法，并提供了有关在各种方案中使用最佳方法的建议。 它还提供有关底层部署技术的详细信息列表和重要详细信息。 

## <a name="deployment-methods"></a>部署方法

用于将代码发布到 Azure 的部署技术通常由您发布应用程序的方式决定。 适当的部署方法取决于特定需求和开发周期中的时间点。 例如，在开发和测试期间，可以直接从开发工具（如 Visual Studio Code）部署。 当你的应用在生产环境中时，你很可能会不断地从源代码管理中进行发布，或者使用自动发布管道（包括附加的验证和测试）。  

下表介绍了可用于函数项目的部署方法。

| 部署 &nbsp; 类型 | 方法 | 最适用于 .。。 |
| -- | -- | -- |
| 基于工具 | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; Code &nbsp; 发布](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio 发布](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[核心工具发布](functions-run-local.md#publish) | 开发和其他 ad hock 部署过程中的部署。 部署由工具在本地进行管理。 | 
| 应用服务-托管| &bull;&nbsp;[部署 &nbsp; 中心 &nbsp; (CI/CD) ](functions-continuous-deployment.md)<br/>&bull;&nbsp;[容器 &nbsp; 部署](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |   (CI/CD) 从源代码管理或从容器注册表进行持续部署。 部署由应用服务平台 (Kudu) 进行管理。|
| 外部管道|&bull;&nbsp;[DevOps 管道](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub 操作](functions-how-to-github-actions.md) | 包括其他验证、测试和其他操作的生产和 DevOps 管道作为自动部署的一部分运行。 部署由管道进行管理。 |

尽管特定函数部署基于其上下文使用最佳技术，但大多数部署方法都是基于 [zip 部署](#zip-deploy)的。

## <a name="deployment-technology-availability"></a>部署技术的可用性

Azure Functions 支持跨平台的本地开发以及使用 Windows 和 Linux 作为托管位置。 目前，可以使用三种托管计划：

+ [消耗](functions-scale.md#consumption-plan)
+ [高级](functions-scale.md#premium-plan)
+ [专用（应用服务）](functions-scale.md#app-service-plan)

每种计划有不同的行为。 并非所有部署技术都适用于每种风格的 Azure Functions。 以下图表显示了哪些部署技术适用于操作系统和托管计划的每种组合：

| 部署技术 | Windows 消耗计划 | Windows 高级 | Windows 专用计划  | Linux 消耗计划 | Linux 高级版 | Linux 专用计划 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 外部包 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| 压缩部署 |✔|✔|✔|✔|✔|✔|
| Docker 容器 | | | | |✔|✔|
| Web 部署 |✔|✔|✔| | | |
| 源代码管理 |✔|✔|✔| |✔|✔|
| 本地 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 云同步<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| 门户编辑 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> 需要[手动触发器同步](#trigger-syncing)的部署技术。
<sup>2</sup> 仅对 Linux 上使用高级和专用计划的 HTTP 和计时器触发器启用门户编辑。

## <a name="key-concepts"></a>关键概念

若要了解 Azure Functions 中的部署工作原理，必须先掌握一些关键概念。

### <a name="trigger-syncing"></a>触发器同步

更改任何触发器时，Functions 基础结构必须意识到这些更改。 对于许多部署技术而言，同步会自动进行。 但在某些情况下，必须手动同步触发器。 通过引用外部包 URL、本地 Git、云同步或 FTP 部署更新时，必须手动同步触发器。 可通过以下三种方式之一来同步触发器：

* 在 Azure 门户中重启函数应用
* 使用[主密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)将 HTTP POST 请求发送到 `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`。
* 将 HTTP POST 请求发送到 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`。 请将占位符替换为你的订阅 ID、资源组名称和函数应用名称。

### <a name="remote-build"></a>远程生成

Azure Functions 可以自动在它在压缩部署后接收的代码上执行生成操作。 这些生成的行为存在轻微差异，具体取决于应用是在 Windows 上还是在 Linux 上运行。 如果此前已将应用设置为在[从包运行](run-functions-from-deployment-package.md)模式下运行，则不会执行远程生成。 若要了解如何使用远程生成，请导航到 [zip 部署](#zip-deploy)。

> [!NOTE]
> 如果存在远程生成问题，可能是因为应用是在该功能发布日期（2019 年 8 月 1 日）之前创建的。 尝试创建新的函数应用，或运行 `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` 更新函数应用。 此命令可能需要尝试两次才能成功。

#### <a name="remote-build-on-windows"></a>Windows 上的远程生成

在 Windows 上运行的所有函数应用都有一个小的管理应用，即 SCM（或 [Kudu](https://github.com/projectkudu/kudu)）站点。 该站点会处理 Azure Functions 的很多部署和生成逻辑。

将应用部署到 Windows 时，会运行特定于语言的命令，例如 `dotnet restore` (C#) 或 `npm install` (JavaScript)。

#### <a name="remote-build-on-linux"></a>Linux 上的远程生成

若要在 Linux 上启用远程生成，必须设置以下[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)：

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

默认情况下，[Azure Functions Core Tools](functions-run-local.md) 和[适用于 Visual Studio Code 的 Azure Functions 扩展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)在部署到 Linux 时执行远程生成。 因此，这两种工具在 Azure 中自动创建这些设置。

在 Linux 上以远程方式生成应用时，应用[从部署包运行](run-functions-from-deployment-package.md)。

##### <a name="consumption-plan"></a>消耗计划

在消耗计划中运行的 Linux 函数应用没有 SCM/Kudu 站点，这限制了部署选项。 但是，在消耗计划中运行的 Linux 上的函数应用支持远程生成。

##### <a name="dedicated-and-premium-plans"></a>专用和高级计划

) 计划和高级计划中 (的 Linux 上运行的函数应用 [计划](functions-scale.md#app-service-plan) 和 [高级计划](functions-scale.md#premium-plan) 还具有受限的 Kudu 站点。

## <a name="deployment-technology-details"></a>部署技术详细信息

Azure Functions 中提供了以下部署方法。

### <a name="external-package-url"></a>外部包 URL

可以使用外部包 URL 来引用包含函数应用的远程包 (.zip) 文件。 可从提供的 URL 下载该文件，应用将在[“从包运行”](run-functions-from-deployment-package.md)模式下运行。

>__如何使用：__ 将 `WEBSITE_RUN_FROM_PACKAGE` 添加到应用程序设置。 此设置的值应是一个 URL（要运行的特定包文件的位置）。 可以[在门户中](functions-how-to-use-azure-function-app-settings.md#settings)或[使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 来添加设置。
>
>如果使用 Azure Blob 存储，请结合[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 使用专用容器，使 Functions 能够访问该包。 每当应用程序重启时，都会提取内容的副本。 引用必须在应用程序的整个生存期内有效。

>__何时使用：__ 如果用户不希望进行[远程生成](#remote-build)，则对于消耗计划中的在 Linux 上运行的 Azure Functions，外部包 URL 是唯一受支持的部署方法。 更新函数应用引用的包文件时，必须[手动同步触发器](#trigger-syncing)，以告知 Azure 你的应用程序已更改。

### <a name="zip-deploy"></a>压缩部署

使用压缩部署可将包含函数应用的 .zip 文件推送到 Azure。 可以选择将应用设置为开始[从包运行](run-functions-from-deployment-package.md)，或者指定进行[远程生成](#remote-build)。

>__如何使用：__ 使用偏爱的客户端工具进行部署：[Visual Studio Code](functions-develop-vs-code.md#publish-to-azure)、[Visual Studio](functions-develop-vs.md#publish-to-azure)，或从命令行使用 [Azure Functions Core Tools](functions-run-local.md#project-file-deployment)。 默认情况下，这些工具使用 zip 部署并[从包运行](run-functions-from-deployment-package.md)。 Core Tools 和 Visual Studio Code 扩展在部署到 Linux 时都启用[远程生成](#remote-build)。 若要手动将 .zip 文件部署到函数应用，请遵照[从 .zip 文件或 URL 进行部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)中的说明操作。

>使用压缩部署方法时，可将应用设置为[从包运行](run-functions-from-deployment-package.md)。 若要从包运行，请将 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置值设置为 `1`。 我们建议使用压缩部署。 此方法可以缩短应用程序加载时间，并且是 VS Code、Visual Studio 和 Azure CLI 的默认部署方法。

>__何时使用：__ 压缩部署是建议用于 Azure Functions 的部署技术。

### <a name="docker-container"></a>Docker 容器

可以部署包含函数应用的 Linux 容器映像。

>__如何使用：__ 在高级或专用计划中创建 Linux 函数应用，并指定要从中运行的容器映像。 可通过两种方式实现此目的：
>
>* 在 Azure 门户上的 Azure 应用服务计划中创建一个 Linux 函数应用。 对于“发布”，请选择“Docker 映像”，然后配置容器。******** 输入映像所在的位置。
>* 使用 Azure CLI 在应用服务计划中创建 Linux 函数应用。 若要了解如何操作，请参阅 [在 Linux 上使用自定义映像创建函数](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)。
>
>若要使用自定义容器部署到现有应用，请在 [Azure Functions Core Tools](functions-run-local.md) 中使用 [`func deploy`](functions-run-local.md#publish) 命令。

>__何时使用它：__ 需要更好地控制运行函数应用的 Linux 环境时，可使用 Docker 容器选项。 此部署机制仅适用于在 Linux 上运行的 Functions。

### <a name="web-deploy-msdeploy"></a>Web 部署 (MSDeploy)

Web 部署可打包 Windows 应用程序（包括 Azure 中的 Windows 上运行的函数应用）并将其部署到任何 IIS 服务器。

>__如何使用：__ 使用 [Visual Studio Tools for Azure Functions](functions-create-your-first-function-visual-studio.md)。 清除“从包文件运行(建议)”复选框。****
>
>还可以下载 [Web 部署 3.6](https://www.iis.net/downloads/microsoft/web-deploy) 并直接调用 `MSDeploy.exe`。

>__何时使用：__ 可以顺利地支持 Web 部署，但是，首选机制是[在启用“从包运行”的情况下使用压缩部署](#zip-deploy)。 有关详细信息，请参阅 [Visual Studio开发指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>源代码管理

使用源代码管理将函数应用连接到 Git 存储库。 更新该存储库中的代码会触发部署。 有关详细信息，请参阅 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__如何使用：__ 使用门户 Functions 区域中的部署中心设置从源代码管理进行发布。 有关详细信息，请参阅 [Azure Functions 的连续部署](functions-continuous-deployment.md)。

>__何时使用：__ 对于协作开发函数应用的团队而言，最佳做法是使用源代码管理。 源代码管理是一种很好的部署选项，可实现更复杂的部署管道。

### <a name="local-git"></a>本地 Git

可以使用本地 Git 将代码从本地计算机推送到 Azure Functions。

>__如何使用：__ 请遵照[使用本地 Git 部署到 Azure 应用服务](../app-service/deploy-local-git.md)中的说明。

>__何时使用：__ 一般而言，我们建议使用其他部署方法。 从本地 Git 发布时，必须[手动同步触发器](#trigger-syncing)。

### <a name="cloud-sync"></a>云同步

使用云同步可将 Dropbox 和 OneDrive 中的内容同步到 Azure Functions。

>__如何使用：__ 按照 [同步云文件夹](../app-service/deploy-content-sync.md)中的内容中的说明进行操作。

>__何时使用：__ 一般而言，我们建议使用其他部署方法。 使用云同步发布时，必须[手动同步触发器](#trigger-syncing)。

### <a name="ftp"></a>FTP

可以使用 FTP 将文件直接传输到 Azure Functions。

>__如何使用：__ 请遵照[使用 FTP/s 部署内容](../app-service/deploy-ftp.md)中的说明。

>__何时使用：__ 一般而言，我们建议使用其他部署方法。 使用 FTP 发布时，必须[手动同步触发器](#trigger-syncing)。

### <a name="portal-editing"></a>门户编辑

在基于门户的编辑器中，可以直接编辑函数应用中的文件（基本上每次保存更改都要进行部署）。

>__如何使用：__ 若要在 Azure 门户中编辑函数，必须事先[在门户中创建函数](functions-create-first-azure-function.md)。 若要保留单一事实源，使用任何其他部署方法会使函数变为只读，并会阻止在门户中继续编辑。 若要恢复到可在 Azure 门户中编辑文件的状态，可以手动将编辑模式改回 `Read/Write`，并删除与部署相关的任何应用程序设置（例如 `WEBSITE_RUN_FROM_PACKAGE`）。

>__何时使用：__ 在门户中可以十分方便地开始使用 Azure Functions。 对于更密集的开发工作，我们建议使用以下客户端工具之一：
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools（命令行）](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

下表显示了支持门户编辑的操作系统和语言：

| Language | Windows 消耗计划 | Windows 高级 | Windows 专用计划 | Linux 消耗计划 | Linux 高级版 | Linux 专用计划 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 脚本 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python（预览版） | | | | | | |
| PowerShell（预览版） |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> 仅对 Linux 上使用高级和专用计划的 HTTP 和计时器触发器启用门户编辑。

## <a name="deployment-behaviors"></a>部署行为

当您执行部署时，所有现有的执行都将被允许完成或超时，在此之后，将加载新的代码以开始处理请求。

如果需要对此转换进行更多控制，则应使用部署槽位。

## <a name="deployment-slots"></a>部署槽

将函数应用部署到 Azure 时，可以部署到单独的部署槽，而不是直接部署到生产槽。 有关部署槽的详细信息，请参阅 [Azure Functions 部署槽](../app-service/deploy-staging-slots.md)文档。

## <a name="next-steps"></a>后续步骤

请阅读以下文章详细了解如何部署函数应用：

+ [Azure Functions 的连续部署](functions-continuous-deployment.md)
+ [使用 Azure DevOps 进行持续交付](functions-how-to-azure-devops.md)
+ [Azure Functions 的压缩部署](deployment-zip-push.md)
+ [从包文件运行 Azure Functions](run-functions-from-deployment-package.md)
+ [为 Azure Functions 中的函数应用自动执行资源部署](functions-infrastructure-as-code.md)

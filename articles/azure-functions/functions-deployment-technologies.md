---
title: Azure Functions 中的部署技术 |Microsoft Docs
description: 了解你可以将代码部署到 Azure Functions 的不同方法的细节。
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 689e54877a5b0a405fe7b3e3981d98ba6b42edb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062941"
---
# <a name="deployment-technologies-in-azure-functions"></a>在 Azure Functions 中的部署技术

有几个不同的技术可用于将 Azure 函数项目代码部署到 Azure。 本文提供了一种技术的详尽列表、 通知的技术有哪些风格的函数、 解释实际发生时的每个方法使用时，并提供有关要在中使用的最佳方法建议各种方案。 支持将部署到 Azure Functions 的各种工具进行了调整到合适的技术根据其上下文。

## <a name="deployment-technology-availability"></a>部署技术可用性

> [!IMPORTANT]
> Azure Functions 支持跨平台本地开发和承载在两个操作系统上：Windows 和 Linux。 有三个托管计划目前不可用，每个都有不同的行为-[消耗](functions-scale.md#consumption-plan)， [Premium](functions-scale.md#premium-plan)，并[专用 （应用服务）](functions-scale.md#app-service-plan)。 并非所有部署技术都都可用于 Azure Functions 的每个风格。

| 部署技术 | Windows 消耗 | Windows 高级 （预览） | Windows 专用  | Linux 消耗 （预览） | Linux 专用 |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| 外部包 URL<sup>1</sup> |✔|✔|✔|✔|✔|
| Zip 部署 |✔|✔|✔| |✔|
| Docker 容器 | | | | |✔|
| Web 部署 |✔|✔|✔| | |
| 源代码管理 |✔|✔|✔| |✔|
| 本地 Git<sup>1</sup> |✔|✔|✔| |✔|
| 云同步<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| 门户编辑 |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup>要求的部署技术[手动触发器同步](#trigger-syncing)。
<sup>2</sup>门户启用编辑为仅 HTTP 和计时器触发器使用专用计划在 Linux 上的函数。

## <a name="key-concepts"></a>关键概念

在继续之前，务必了解一些关键概念，将了解如何部署 Azure Functions 中的工作原理的关键。

### <a name="trigger-syncing"></a>触发同步

在触发器的任何更改时，函数基础结构必须是了解这些更改。 此同步操作自动进行部署的许多技术。 但是，在某些情况下您必须手动同步你的触发器。 使用外部包 URL、 本地 Git、 云同步或 FTP 将更新部署时，您必须确保手动同步你的触发器。 你可以同步中有以下三种触发器：

* 在 Azure 门户中重新启动 function app
* 发送到 HTTP POST 请求`https://www.{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`使用[主密钥](functions-bindings-http-webhook.md#authorization-keys)。
* 发送到 HTTP POST 请求`https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`。 将占位符替换为你的订阅 ID、 资源组名称和函数应用的名称。

## <a name="deployment-technology-details"></a>部署技术详细信息  

### <a name="external-package-url"></a>外部包 URL

__它执行的操作：__ 可以引用包含函数应用的远程包 (.zip) 文件。 从提供的 URL 下载文件和应用程序中运行[运行从包](run-functions-from-deployment-package.md)模式。

__如何使用它：__ 添加`WEBSITE_RUN_FROM_PACKAGE`到应用程序设置。 此设置的值应为 URL-你想要运行特定的包文件的位置。 你可以将设置添加任一[门户中](functions-how-to-use-azure-function-app-settings.md#settings)或[使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)。 如果使用 Azure blob 存储，则应使用具有专用容器[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas)使函数能够访问包。 随时在应用程序重新启动它将提取的内容，这意味着，你的引用必须是有效的应用程序生存期内的副本。

__何时使用它：__ 这是支持的 Azure Functions 消耗计划 （预览版） 中的 Linux 上运行的仅限的部署方法。 在更新函数应用所引用的包文件时，您必须[手动同步触发器](#trigger-syncing)告诉 Azure 你的应用程序已更改。

### <a name="zip-deploy"></a>Zip 部署

__它执行的操作：__ 使用此选项可以将推送包含到 Azure function app 的 zip 文件。 （可选） 还可以指定要在中启动应用程序[运行从包](run-functions-from-deployment-package.md)模式。

__如何使用它：__ 使用你最喜爱的部署客户端工具- [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)， [Visual Studio](functions-develop-vs.md#publish-to-azure)，或[Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)。 若要手动将 zip 文件部署到 function app，请按照的说明，请参阅[从 zip 文件或 url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

此外，通过 zip 部署在部署时，用户可以指定用于从运行其应用[运行从包](run-functions-from-deployment-package.md)模式通过设置`WEBSITE_RUN_FROM_PACKAGE`应用程序设置值为`1`。 此选项，建议使用，并生成您的应用程序的速度更快加载时间。 这是默认情况下，更高版本的客户端工具。

__何时使用它：__ 这是 Windows 上, 运行的 Azure Functions 和 Azure Functions 中的专用计划在 Linux 上运行的建议的部署技术。

### <a name="docker-container"></a>Docker 容器

__它执行的操作：__ 指定 Azure Functions 将请求和运行的容器映像。

__如何使用它：__ 创建 Linux 函数应用在专用的计划中，并指定要从运行的容器映像。 可通过两种方式实现此目的：

* 在 Azure 门户中的应用服务计划上创建 Linux 函数应用。 选择**Docker 映像**有关**发布**，并配置容器，提供托管该映像的位置。
* 在应用服务计划中通过 Azure CLI 创建 Linux 函数应用。 了解如何通过查看[Linux 上使用自定义映像创建一个函数](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)。

若要将部署到现有应用程序使用自定义容器，请使用[ `func deploy` ](functions-run-local.md#publish)命令[Azure Functions Core Tools](functions-run-local.md)。

__何时使用它：__ 使用此选项需要更好地控制在 Linux 环境时运行函数应用。 此部署机制功能仅适用于 Linux 上运行的应用服务计划中的函数。

### <a name="web-deploy-msdeploy"></a>Web 部署 (MSDeploy)

__它执行的操作：__ 打包并部署到任何 IIS 服务器-包括在 Windows 上运行的 Azure 函数应用在 Windows 应用程序。

__如何使用它：__ 使用[Azure Functions 的 Visual Studio 工具](functions-create-your-first-function-visual-studio.md)，并没有刻度线`Run from package file (recommended)`复选框。

或者，调用`MSDeploy.exe`下载后直接[Web 部署 3.6](https://www.iis.net/downloads/microsoft/web-deploy)。

__何时使用它：__ 此部署技术支持并且没有问题，但首选的机制是现在[Zip 运行从随包一起部署启用](#zip-deploy)。 若要了解详细信息，请访问[Visual Studio 开发指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>源代码管理

__它执行的操作：__ 可以将函数应用挂接到 git 存储库，以便对该存储库中的代码的任何更新会触发部署。 有关详细信息，看一看[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

__如何使用它：__ 使用 Azure Functions 门户中的部署中心若要设置从源代码管理发布。 有关详细信息，请参阅 [Azure Functions 的连续部署](functions-continuous-deployment.md)。

__何时使用它：__ 使用源代码管理是团队协作的函数应用的最佳做法，这是使更复杂的部署管道是好的选择。

### <a name="local-git"></a>本地 git

__它执行的操作：__ 允许从本地计算机代码推送到 Azure Functions 使用 Git。

__如何使用它：__ 按照的说明[本地 Git 部署到 Azure 应用服务](../app-service/deploy-local-git.md)。

__何时使用它：__ 一般情况下，建议使用其他部署方法。 当从本地 git 进行发布，您必须[手动同步触发器](#trigger-syncing)。

### <a name="cloud-sync"></a>云同步

__它执行的操作：__ 可以将内容同步 Dropbox 和 OneDrive 中到 Azure Functions。

__如何使用它：__ 按照中的说明[从云文件夹同步内容](../app-service/deploy-content-sync.md)。

__何时使用它：__ 一般情况下，建议使用其他部署方法。 在发布使用云同步时，您必须[手动同步触发器](#trigger-syncing)。

### <a name="ftp"></a>FTP

__它执行的操作：__ 使您能够直接传输到 Azure Functions 的文件。

__如何使用它：__ 按照中的说明[将使用 FTP/s 的内容部署](../app-service/deploy-ftp.md)。

__何时使用它：__ 一般情况下，建议使用其他部署方法。 在发布使用 FTP 时，您必须[手动同步触发器](#trigger-syncing)。

### <a name="portal-editing"></a>门户编辑

__它执行的操作：__ 使用基于门户的编辑器，可直接编辑在 function app 上的文件 (实质上部署任何时候单击**保存**)。

__如何使用它：__ 若要能够编辑您的函数在 Azure 门户中，您必须具有[在门户中创建函数](functions-create-first-azure-function.md)。 使用任何其他部署方法使只读函数，并防止继续门户编辑，以保留单一事实来源。 若要返回到可以在其中编辑你使用 Azure 门户中的文件的状态，您可以手动打开编辑模式回`Read/Write`并删除任何与部署相关应用程序设置 (如`WEBSITE_RUN_FROM_PACKAGE`)。

#### <a name="portal-editing-availability"></a>编辑可用性的门户

| | Windows 消耗 | Windows 高级 （预览） | Windows 专用 | Linux 消耗 （预览） | Linux 专用 |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>1</sup>|
| TypeScript (Node.js) | | | | | |
| C# | | | | | |
| C# 脚本 |✔|✔|✔| |✔<sup>1</sup>|
| F# | | | | | |
| Java | | | | | |
| Python（预览版） | | | | | |
| PowerShell （预览版） |✔|✔|✔| | |

<sup>1</sup>门户启用编辑为仅 HTTP 和计时器触发器使用专用计划在 Linux 上的函数。

__何时使用它：__ 该门户是 Azure Functions 入门的好办法，但是对于任何使用客户端的更强烈的开发工作的工具建议：

* [了解如何使用 VS Code](functions-create-first-function-vs-code.md)
* [开始使用 Azure Functions Core Tools](functions-run-local.md)
* [开始使用 Visual Studio](functions-create-your-first-function-visual-studio.md)

## <a name="other-relevant-information"></a>其他相关信息

### <a name="deployment-slots"></a>部署槽

将函数应用部署到 Azure 时，你可以部署到单独的部署槽而不是直接到生产环境。 部署槽位的详细信息，请参阅[Azure 应用服务槽文档](../app-service/deploy-staging-slots.md)。

#### <a name="deployment-slots-levels-of-support"></a>部署槽支持的级别

有两个级别的支持：

* _正式发布 (GA)_ - 完全支持并获得批准在生产中使用。
* _预览_ - 尚不支持，但将来应达到 GA 状态。

| OS/托管计划 | 级别的支持 |
| --------------- | ------ |
| Windows 消耗 | 预览 |
| Windows 高级 （预览） | 预览 |
| Windows 专用 | 正式发布 |
| Linux 消耗 | 不支持 |
| Linux 专用 | 正式发布 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)
> [使用 Azure DevOps 实现持续交付](functions-how-to-azure-devops.md)
> [Zip 部署 Azure functions](deployment-zip-push.md) 
> [从包文件中运行 Azure Functions](run-functions-from-deployment-package.md)
> [函数应用在 Azure Functions 中的自动化资源部署](functions-infrastructure-as-code.md)

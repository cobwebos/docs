---
title: 在 Azure Functions 中的部署技术 |Microsoft Docs
description: 了解不同方法，可以将代码部署到 Azure Functions。
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594397"
---
# <a name="deployment-technologies-in-azure-functions"></a>在 Azure Functions 中的部署技术

可以使用几个不同的技术将你的 Azure 函数项目代码部署到 Azure。 这篇文章提供了一种技术的详尽列表，介绍了可用于哪些类型的函数哪些技术、 解释了使用每种方法，会发生什么情况以及提供有关在各种方案中使用的最佳方法建议. 支持将部署到 Azure Functions 的各种工具进行了调整到合适的技术根据其上下文。

## <a name="deployment-technology-availability"></a>部署技术可用性

> [!IMPORTANT]
> Azure Functions 支持跨平台本地开发和承载在 Windows 和 Linux 上。 目前，三个托管计划都可用：[消耗](functions-scale.md#consumption-plan)， [Premium](functions-scale.md#premium-plan)，和[专用 （Azure 应用服务）](functions-scale.md#app-service-plan)。 每个计划具有不同的行为。 并非所有部署技术都都可用于 Azure Functions 的每个风格。

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
<sup>2</sup>门户启用编辑仅对于 HTTP 和计时器触发器使用专用的计划在 Linux 上的函数。

## <a name="key-concepts"></a>关键概念

一些关键概念是了解如何部署 Azure Functions 中的工作原理的关键。

### <a name="trigger-syncing"></a>触发同步

在触发器的任何更改时，函数基础结构必须知道所做的更改。 同步操作自动进行部署的许多技术。 但是，在某些情况下，则必须手动同步你的触发器。 引用外部包 URL、 本地 Git、 云同步或 FTP 部署更新时则必须手动同步你的触发器。 可以同步中有以下三种触发器：

* 在 Azure 门户中重新启动 function app
* 发送到 HTTP POST 请求`https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`使用[主密钥](functions-bindings-http-webhook.md#authorization-keys)。
* 发送到 HTTP POST 请求`https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`。 将占位符替换为你的订阅 ID、 资源组名称和函数应用的名称。

## <a name="deployment-technology-details"></a>部署技术详细信息 

Azure Functions 中提供了下列部署方法。

### <a name="external-package-url"></a>外部包 URL

您可以使用外部包 URL 引用包含函数应用的远程包 (.zip) 文件。 从提供的 URL 下载文件和应用程序中运行[运行从包](run-functions-from-deployment-package.md)模式。

>__如何使用它：__ 添加`WEBSITE_RUN_FROM_PACKAGE`到应用程序设置。 此设置的值应为 URL （你想要运行特定的包文件的位置）。 你可以将设置添加任一[门户中](functions-how-to-use-azure-function-app-settings.md#settings)或[使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)。 
>
>如果使用 Azure Blob 存储，使用具有专用容器[共享的访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)使函数能够访问包。 任何时候，只要应用程序重新启动，它提取内容的副本。 你的引用的应用程序生存期内必须是有效的。

>__何时使用它：__ 外部包 URL 是 Azure Functions 消耗计划 （预览版） 中运行在 Linux 上的唯一受支持的部署方法。 更新函数应用引用的包文件时，您必须[手动同步触发器](#trigger-syncing)告诉 Azure 你的应用程序已更改。

### <a name="zip-deploy"></a>Zip 部署

使用 zip 部署要推送的.zip 文件中包含到 Azure function app。 或者，可以设置您的应用程序中启动[运行从包](run-functions-from-deployment-package.md)模式。

>__如何使用它：__ 使用你喜爱的客户端工具部署：[VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)， [Visual Studio](functions-develop-vs.md#publish-to-azure)，则[Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)。 若要手动将.zip 文件部署到 function app，请按照中的说明[部署.zip 文件或 URL 从](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。
>
>使用 zip 部署时部署，您可以设置您的应用程序中运行[运行从包](run-functions-from-deployment-package.md)模式。 若要设置从包中运行模式下，设置`WEBSITE_RUN_FROM_PACKAGE`应用程序将值设置为`1`。 我们建议 zip 部署。 它将产生更快的加载时间为您的应用程序，并且 VS Code、 Visual Studio 和 Azure CLI 的默认值。

>__何时使用它：__ Zip 部署在 Windows 上运行的 Azure functions 和 Azure Functions 中的专用计划在 Linux 上运行的是建议的部署技术。

### <a name="docker-container"></a>Docker 容器

您可以部署包含 function app 的 Linux 容器映像。

>__如何使用它：__ 专用计划中创建 Linux 函数应用，并指定要从运行的容器映像。 可通过两种方式实现此目的：
>
>* 在 Azure 门户中的 Azure 应用服务计划上创建 Linux 函数应用。 有关**发布**，选择**Docker 映像**，然后配置该容器。 输入托管该映像的位置。
>* 使用 Azure CLI 在应用服务计划上创建 Linux 函数应用。 若要了解如何操作，请参阅[使用自定义映像在 Linux 上创建一个函数](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)。
>
>若要将部署到现有应用程序中使用自定义容器[Azure Functions Core Tools](functions-run-local.md)，使用[ `func deploy` ](functions-run-local.md#publish)命令。

>__何时使用它：__ 使用函数应用在其中运行 Docker 容器选项时需要更好地控制在 Linux 环境。 此部署机制是仅适用于 Linux 上运行的应用服务计划中的函数。

### <a name="web-deploy-msdeploy"></a>Web 部署 (MSDeploy)

Web 部署包并部署到任何 IIS 服务器，包括你在 Azure 中的 Windows 上运行的函数应用在 Windows 应用程序。

>__如何使用它：__ 使用[Azure Functions 的 Visual Studio 工具](functions-create-your-first-function-visual-studio.md)。 清除**从包文件 （推荐） 运行**复选框。
>
>此外可以下载[Web 部署 3.6](https://www.iis.net/downloads/microsoft/web-deploy) ，并调用`MSDeploy.exe`直接。

>__何时使用它：__ Web 部署支持并且没有问题，但首选的机制是[使用运行从启用的包部署 zip](#zip-deploy)。 若要了解详细信息，请参阅[Visual Studio 开发指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>源代码管理

使用源控件将函数应用程序连接到 Git 存储库。 对该存储库中的代码的更新会触发部署。 有关详细信息，请参阅[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__如何使用它：__ 使用 Azure Functions 门户中的部署中心若要设置从源代码管理发布。 有关详细信息，请参阅 [Azure Functions 的连续部署](functions-continuous-deployment.md)。

>__何时使用它：__ 使用源代码管理是在其函数应用进行协作的团队的最佳做法。 源代码管理是一个良好的部署选项，使更复杂的部署管道。

### <a name="local-git"></a>本地 Git

您可以使用本地 Git 推送代码到本地计算机到 Azure Functions 中使用 Git。

>__如何使用它：__ 按照中的说明[本地 Git 部署到 Azure 应用服务](../app-service/deploy-local-git.md)。

>__何时使用它：__ 一般情况下，我们建议你使用不同的部署方法。 当从本地 Git 进行发布时，您必须[手动同步触发器](#trigger-syncing)。

### <a name="cloud-sync"></a>云同步

使用云同步到同步到 Azure Functions 从 Dropbox 和 OneDrive 内容。

>__如何使用它：__ 按照中的说明[从云文件夹同步内容](../app-service/deploy-content-sync.md)。

>__何时使用它：__ 一般情况下，我们建议其他部署方法。 在发布使用云同步时，您必须[手动同步触发器](#trigger-syncing)。

### <a name="ftp"></a>FTP

您可以使用 FTP 将文件直接传输到 Azure Functions。

>__如何使用它：__ 按照中的说明[通过使用 FTP/s 部署内容](../app-service/deploy-ftp.md)。

>__何时使用它：__ 一般情况下，我们建议其他部署方法。 当使用 FTP 发布时，您必须[手动同步触发器](#trigger-syncing)。

### <a name="portal-editing"></a>门户编辑

在基于门户的编辑器中，可以直接编辑 （实质上部署每次保存所做的更改） 在函数应用中的文件。

>__如何使用它：__ 若要能够编辑您的函数在 Azure 门户中，您必须具有[在门户中创建函数](functions-create-first-azure-function.md)。 若要保留单一事实来源，使用任何其他部署方法使函数成为只读的和阻止持续门户编辑。 若要返回到可以在其中编辑你在 Azure 门户中的文件的状态，您可以手动打开编辑模式回`Read/Write`并删除任何与部署相关应用程序设置 (如`WEBSITE_RUN_FROM_PACKAGE`)。 

>__何时使用它：__ 该门户是 Azure Functions 入门的好方法。 对于更密集的开发工作，我们建议你使用客户端工具：
>
>* [了解如何使用 VS Code](functions-create-first-function-vs-code.md)
>* [开始使用 Azure Functions Core Tools](functions-run-local.md)
>* [开始使用 Visual Studio](functions-create-your-first-function-visual-studio.md)

下表显示了操作系统和语言支持门户编辑：

| | Windows 消耗 | Windows 高级 （预览） | Windows 专用 | Linux 消耗 （预览） | Linux 专用 |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# 脚本 |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python（预览版） | | | | | |
| PowerShell （预览版） |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> 对于使用专用的计划在 Linux 上的函数，仅对于 HTTP 和计时器触发器已启用门户编辑。

## <a name="deployment-slots"></a>部署槽

将函数应用部署到 Azure 时，你可以部署到单独的部署槽而不是直接向生产部署。 部署槽位的详细信息，请参阅[Azure 应用服务槽](../app-service/deploy-staging-slots.md)。

### <a name="deployment-slots-levels-of-support"></a>部署槽支持的级别

有两个级别的部署槽的支持：

* **正式版 (GA)** :完全支持的和已批准用于生产。
* **预览**：尚不受支持，但预期将来达到 GA 状态。

| OS/托管计划 | 级别的支持 |
| --------------- | ------ |
| Windows 消耗 | 预览 |
| Windows 高级 （预览） | 预览 |
| Windows 专用 | 正式版 |
| Linux 消耗 | 不支持 |
| Linux 专用 | 正式版 |

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解有关部署函数应用的详细信息： 

+ [Azure Functions 的连续部署](functions-continuous-deployment.md)
+ [通过使用 Azure DevOps 持续交付](functions-how-to-azure-devops.md)
+ [Azure Functions 的 zip 部署](deployment-zip-push.md)
+ [从包文件中运行 Azure Functions](run-functions-from-deployment-package.md)
+ [Azure Functions 中的函数应用的自动化资源部署](functions-infrastructure-as-code.md)

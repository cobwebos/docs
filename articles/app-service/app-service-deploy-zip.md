---
title: "使用 zip 文件将应用部署到 Azure 应用服务 | Microsoft Docs"
description: "了解如何使用 zip 文件将应用部署到 Azure 应用服务。"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>使用 zip 文件将应用部署到 Azure 应用服务

本文演示如何使用 zip 文件将 Web 应用部署到 [Azure 应用服务](app-service-web-overview.md)。 

此 .zip 文件部署使用相同的 Kudu 服务，支持基于持续集成的部署。 对于 .zip 文件部署，Kudu 支持以下功能： 

- 删除先前的部署留下的文件。
- 启用默认生成过程的选项，包括包还原。
- [部署自定义](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)，包括运行部署脚本。  
- 部署日志。 

有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤：

* [创建应用服务应用](/azure/app-service/)，或使用为另一教程创建的应用。

## <a name="create-a-project-zip-file"></a>创建一个项目 zip 文件

>[!NOTE]
> 如果以 zip 文件的形式下载文件，请首先提取该文件。 例如，如果从 GitHub 下载了 zip 文件，则无法按原样部署该文件。 GitHub 可添加额外的嵌套目录，但这不适用于应用服务。 
>

在本地终端窗口中，导航到应用项目的根目录。 

此目录应包含 Web 应用的入口文件，例如 _index.html_、_index.php_ 和 _app.js_。 它还可能包含包管理文件，如 _project.json_、_composer.json_、_package.json_、_bower.json_ 和 _requirements.txt_。

创建一个包含项目中的所有内容的 zip 存档。 以下命令使用终端中的默认工具：

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>将 zip 文件上传到 Cloud Shell

如果改为选择从本地终端运行 Azure CLI，请跳过此步骤。

按此处的步骤操作，将 zip 文件上传到 Cloud Shell。 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

有关详细信息，请参阅[在 Azure Cloud Shell 中持久保存文件](../cloud-shell/persisting-shell-storage.md)。

## <a name="deploy-zip-file"></a>部署 ZIP 文件

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) 命令将上传的 zip 文件部署到 Web 应用。 如果选择不使用 Cloud Shell，请确保 Azure CLI 的版本为 2.0.21 或更高版本。 要查看已有版本，请在本地终端窗口中运行 `az --version` 命令。 

下面的示例部署上传的 zip 文件。 使用 Azure CLI 的本地安装时，请为 `--src` 指定本地 zip 文件的路径。   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

此命令将 zip 文件中的文件和目录部署到默认的应用服务应用程序文件夹 (`\home\site\wwwroot`) 并重启应用。 如果配置了任何其他自定义生成过程，则也会运行该过程。 有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

要查看此应用的部署列表，必须使用 REST API（请参阅下一节）。 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>后续步骤

有关更高级的部署方案，请参阅[使用 Git 部署到 Azure](app-service-deploy-local-git.md)。 使用 Git 部署到 Azure 支持版本控制、包还原、MSBuild 等。

## <a name="more-resources"></a>更多资源

* [Kudu：从 zip 文件部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure 应用服务部署凭据](app-service-deploy-ftp.md)

---
title: Azure Functions 的 Zip 推送部署 | Microsoft Docs
description: 使用 Kudu 部署服务的 .zip 文件部署功能来发布 Azure Functions。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: 2762e5c4f2b67415a0e42e80a34ae5b34c57adc9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086575"
---
# <a name="zip-deployment-for-azure-functions"></a>Azure Functions 的 Zip 部署

本文介绍如何将函数应用项目文件从 .zip（压缩）文件部署到 Azure。 可在本文中了解如何通过 Azure CLI 和 REST API 进行推送部署。 [Azure Functions 核心工具](functions-run-local.md)在将本地项目发布到 Azure 时也使用这些部署 API。

Azure Functions 具有完整的持续部署范围，以及由 Azure 应用服务提供的集成选项。 有关详细信息，请参阅 [Azure Functions 的连续部署](functions-continuous-deployment.md)。

要加快开发速度，你可能会发现直接通过 .zip 文件部署你的函数应用项目文件更为简单。 .zip 部署 API 接受 .zip 文件的内容并将内容提取到你的函数应用的 `wwwroot` 文件夹中。 此 .zip 文件部署使用相同的 Kudu 服务，支持基于持续集成的部署，包括：

+ 删除之前的部署留下的文件。
+ 部署自定义，包括运行部署脚本。
+ 部署日志。
+ 在[消耗计划](functions-scale.md)函数应用中同步函数触发器。

有关详细信息，请参阅 [.zip 部署参考](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

## <a name="deployment-zip-file-requirements"></a>部署.zip 文件的要求

用于推送部署的 .zip 文件必须包含运行你的函数时所需的所有文件。

>[!IMPORTANT]
> 使用 .zip 部署时，从函数应用删除现有部署中未包含在 .zip 文件内的任何文件。  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

函数应用包含 `wwwroot` 目录中的所有文件和文件夹。 .zip 文件部署包含 `wwwroot` 目录的内容，但不包含目录本身。 部署 C# 类库项目时，必须在 .zip 包中的 `bin` 子文件夹中包括编译的库文件和依赖项。

## <a name="download-your-function-app-files"></a>下载函数应用文件

在本地计算机上进行开发时，可以轻松在该开发计算机上创建函数应用项目文件夹的 .zip 文件。

但是，可能已通过 Azure 门户中的编辑器创建了函数。 可以通过以下方式之一下载现有的函数应用项目：

+ **通过 Azure 门户：**

  1. 登录 [Azure 门户](https://portal.azure.com)，并转到函数应用。

  2. 在“概述”选项卡中，选择“下载应用内容”。 选择下载选项，然后选择“下载”。

      ![下载函数应用项目](./media/deployment-zip-push/download-project.png)

     所下载的 .zip 文件格式正确，可使用 .zip 推送部署将其重新发布到函数应用。 门户下载还可以添加在 Visual Studio 中直接打开函数应用所需的文件。

+ **使用 REST API：**

    使用以下部署 GET API 从 `<function_app>` 项目中下载文件： 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    包含 `/site/wwwroot/` 可确保 zip 文件仅包含函数应用项目文件，而不是整个站点。 如果尚未登录 Azure，系统会要求你登录。  

还可从 GitHub 存储库下载 .zip 文件。 在以 .zip 文件形式下载 GitHub 存储库时，GitHub 会为分支添加一个额外的文件夹级别。 此额外的文件夹级别意味着无法直接部署从 GitHub 下载的 .zip 文件。 如果当前使用 GitHub 存储库维护函数应用，应使用[持续集成](functions-continuous-deployment.md)来部署应用。  

## <a name="cli"></a>使用 Azure CLI 进行部署

可使用 Azure CLI 来触发推送部署。 使用 [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) 命令将 .zip 文件推送部署到函数应用。 要使用此命令，必须使用 Azure CLI 版本 2.0.21 或更高版本。 要查看当前使用的 Azure CLI 版本，请使用 `az --version` 命令。

以下命令将 `<zip_file_path>` 占位符替换为 .zip 文件的位置路径。 此外，`<app_name>` 替换为函数应用的唯一名称。 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

此命令将项目文件从下载的 .zip 文件部署到 Azure 中的函数应用。 然后重启应用。 要查看此函数应用的部署列表，必须使用 REST API。

在本地计算机上使用 Azure CLI 时，`<zip_file_path>` 是计算机中 .zip 文件的路径。 也可以在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行 Azure CLI。 使用 Cloud Shell 时，首先必须将部署 .zip 文件上传到与 Cloud Shell 关联的 Azure 文件帐户。 在这种情况下，`<zip_file_path>` 是 Cloud Shell 帐户使用的存储位置。 有关详细信息，请参阅[在 Azure Cloud Shell 中持久保存文件](../cloud-shell/persisting-shell-storage.md)。

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>从部署包中运行函数

还可以选择直接从部署包文件中运行你的函数。 此方法跳过了将文件从包中复制到函数应用的 `wwwroot` 目录这一部署步骤。 包文件将由 Functions 运行时进行装载，并且 `wwwroot` 目录的内容将变为只读的。  

Zip 部署集成了此功能，可以通过将函数应用设置 `WEBSITE_RUN_FROM_PACKAGE` 设置为 `1` 值来启用此功能。 有关详细信息，请参阅[从部署包文件运行函数](run-functions-from-deployment-package.md)。

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file

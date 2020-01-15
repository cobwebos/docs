---
title: 使用 ZIP 或 WAR 文件部署代码
description: 了解如何使用 ZIP 文件（或者对于 Java 开发人员而言使用 WAR 文件）将应用部署到 Azure 应用服务。
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945174"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>使用 ZIP 或 WAR 文件将应用部署到 Azure 应用服务

本文演示如何使用 ZIP 或 WAR 文件将 Web 应用部署到 [Azure 应用服务](overview.md)。 

此 .zip 文件部署使用相同的 Kudu 服务，支持基于持续集成的部署。 对于 .zip 文件部署，Kudu 支持以下功能： 

- 删除先前的部署留下的文件。
- 启用默认生成过程的选项，包括包还原。
- 部署自定义，包括运行部署脚本。  
- 部署日志。 
- 文件大小限制为 2048 MB。

有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

WAR 文件部署将 [WAR](https://wikipedia.org/wiki/WAR_(file_format)) 文件部署到应用服务，以运行 Java Web 应用。 请参阅[部署 WAR 文件](#deploy-war-file)。

## <a name="prerequisites"></a>必备组件

若要完成本文中的步骤，请[创建应用服务应用](/azure/app-service/)，或者使用为其他教程创建的应用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
上述终结点目前不适用于 Linux 应用服务。 请考虑改用 FTP 或[ZIP 部署 API](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) 。

## <a name="deploy-zip-file-with-azure-cli"></a>使用 Azure CLI 部署 ZIP 文件

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令将上传的 zip 文件部署到 Web 应用。  

下面的示例部署上传的 zip 文件。 使用 Azure CLI 的本地安装时，请为 `--src` 指定本地 zip 文件的路径。

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

此命令将 zip 文件中的文件和目录部署到默认的应用服务应用程序文件夹 (`\home\site\wwwroot`) 并重启应用。

默认情况下，部署引擎假定 ZIP 文件已准备就绪，可以按原样运行，不会运行任何生成自动化。 若要启用与[Git 部署](deploy-local-git.md)相同的生成自动化，请在[Cloud Shell](https://shell.azure.com)中运行以下命令，设置 `SCM_DO_BUILD_DURING_DEPLOYMENT` 应用设置：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>部署 WAR 文件

若要将 WAR 文件部署到应用服务，请将 POST 请求发送到 `https://<app-name>.scm.azurewebsites.net/api/wardeploy`。 POST 请求必须在消息正文中包含此 .war 文件。 应用的部署凭据是通过使用 HTTP BASIC 身份验证在请求中提供的。

部署 WAR 文件时，请始终使用 `/api/wardeploy`。 此 API 将展开 WAR 文件并将其放在共享文件驱动器上。 使用其他部署 Api 可能会导致不一致的行为。 

对于 HTTP 基本身份验证，需使用应用服务部署凭据。 若要了解如何设置部署凭据，请参阅[设置和重置用户级别凭据](deploy-configure-credentials.md#userscope)。

### <a name="with-curl"></a>使用 cURL

以下示例使用 cURL 工具部署 .war 文件。 替换占位符 `<username>`、`<war-file-path>` 和 `<app-name>`。 出现 cURL 提示时，键入密码。

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>使用 PowerShell

下面的示例使用[AzWebapp](/powershell/module/az.websites/publish-azwebapp)上传 war 文件。 替换占位符 `<group-name>`、`<app-name>` 和 `<war-file-path>`。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>后续步骤

有关更高级的部署方案，请参阅[使用 Git 部署到 Azure](deploy-local-git.md)。 使用 Git 部署到 Azure 支持版本控制、包还原、MSBuild 等。

## <a name="more-resources"></a>更多资源

* [Kudu：从 zip 文件部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure 应用服务部署凭据](deploy-ftp.md)

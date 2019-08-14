---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 4f3236c0a167a2b6f7586c6cb5fea8e30f55a86c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954058"
---
## <a name="rest"></a>使用 REST API 部署 ZIP 文件 

可以使用[部署服务 REST API](https://github.com/projectkudu/kudu/wiki/REST-API) 将 .zip 文件部署到 Azure 中的应用。 若要部署，请将 POST 请求发送到 https://<app_name>.scm.azurewebsites.net/api/zipdeploy。 POST 请求必须在消息正文中包含此 .zip 文件。 应用的部署凭据是通过使用 HTTP BASIC 身份验证在请求中提供的。 有关详细信息，请参阅 [.zip 推送部署参考](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。 

对于 HTTP 基本身份验证，需使用应用服务部署凭据。 若要了解如何设置部署凭据，请参阅[设置和重置用户级别凭据](../articles/app-service/deploy-configure-credentials.md#userscope)。

### <a name="with-curl"></a>使用 cURL

以下示例使用 cURL 工具部署 .zip 文件。 替换占位符 `<username>`、`<password>`、`<zip_file_path>` 和 `<app_name>`。 出现 cURL 提示时，键入密码。

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

此请求从已上传的 .zip 文件触发推送部署。 可以使用 `https://<app_name>.scm.azurewebsites.net/api/deployments` 终结点查看当前和之前的部署，如以下 cURL 示例所示。 同样，使用应用的名称替换 `<app_name>`；使用部署凭据的用户名替换 `<deployment_user>`。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>使用 PowerShell

下面的示例使用[AzWebapp](/powershell/module/az.websites/publish-azwebapp)上传 .zip 文件。 替换占位符 `<group-name>`、`<app-name>` 和 `<zip-file-path>`。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

此请求从已上传的 .zip 文件触发推送部署。 

若要查看当前和之前的部署，请运行以下命令。 再次替换`<deployment-user>`、 `<deployment-password>`和`<app-name>`占位符。

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```

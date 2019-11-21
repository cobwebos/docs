---
title: Configure function app settings in Azure
description: 了解如何配置 Azure Function App 设置。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230578"
---
# <a name="manage-your-function-app"></a>Manage your function app 

在 Azure Functions 中，Function App 提供各个函数的执行上下文。 Function App 行为适用于由给定 Function App 托管的所有函数。 All functions in a function app must be of the same [language](supported-languages.md). 

Individual functions in a function app are deployed together and are scaled together. All functions in the same function app share resources, per instance, as the function app scales. 

Connection strings, environment variables, and other application settings are defined separately for each function app. Any data that must be shared between function apps should be stored externally in a persisted store.

This article describes how to configure and manage your function apps. 

> [!TIP]  
> Many configuration options can also be managed by using the [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>在 Azure 门户中开始

要开始，请转到 [Azure 门户]，并使用 Azure 帐户登录。 在门户顶端的搜索栏中，键入函数应用的名称，并从列表中将其选中。 选择 Function App 后，将看到以下页面：

![Azure 门户中 Function App 的概述](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

You can navigate to everything you need to manage your function app from the overview page, in particular the **[Application settings](#settings)** and **[Platform features](#platform-features)** .

## <a name="settings"></a>应用程序设置

The **Application Settings** tab maintains settings that are used by your function app. These settings are stored encrypted, and you must select **Show values** to see the values in the portal. You can also access application settings by using the Azure CLI.

### <a name="portal"></a>门户

To add a setting in the portal, select **New application setting** and add the new key-value pair.

![Function app settings in the Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

The [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) command returns the existing application settings, as in the following example:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

The [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command adds or updates an application setting. The following example creates a setting with a key named `CUSTOM_FUNCTION_APP_SETTING` and a value of `12345`:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Use application settings

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

When you develop a function app locally, you must maintain local copies of these values in the local.settings.json project file. To learn more, see [Local settings file](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Platform features

![Function App 平台功能选项卡。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Function App 运行于 Azure 应用服务平台，并由该平台维护。 在这种情况下，Function App 有权访问 Azure 核心 Web 托管平台的大多数功能。 可在“平台功能”选项卡中访问应用服务平台中许多可用于 Function App 的功能。 

> [!NOTE]
> Function App 运行于消耗托管计划中时，并非所有应用服务功能均可用。

The rest of this article focuses on the following App Service features in the Azure portal that are useful for Functions:

+ [应用服务编辑器](#editor)
+ [Console](#console)
+ [高级工具 (Kudu)](#kudu)
+ [部署选项](#deployment)
+ [CORS](#cors)
+ [身份验证](#auth)

若要深入了解如何使用应用服务设置，请参阅[配置 Azure 应用服务设置](../app-service/configure-common.md)。

### <a name="editor"></a>应用服务编辑器

![应用服务编辑器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

应用服务编辑器是一种高级的门户内编辑器，可用于修改诸如 JSON 配置文件和代码文件等内容。 选择此选项会启动单独的浏览器选项卡和基本编辑器。 借此，可与 Git 存储库集成、运行和调试代码，并可修改 Function App 设置。 This editor provides an enhanced development environment for your functions compared with the built-in function editor.  

We recommend that you consider developing your functions on your local computer. When you develop locally and publish to Azure, your project files are read-only in the portal. To learn more, see [Code and test Azure Functions locally](functions-develop-local.md).

### <a name="console"></a>控制台

![Function App 控制台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

要从命令行与 Function App 交互时，门户内控制台就是非常合适的开发人员工具。 常见命令包括创建和导航目录与文件，以及执行批处理文件和脚本。 

When developing locally, we recommend using the [Azure Functions Core Tools](functions-run-local.md) and the [Azure CLI].

### <a name="kudu"></a>高级工具 (Kudu)

![配置 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

应用服务的高级工具（也称为 Kudu）提供对 Function App 高级管理功能的访问。 从 Kudu 中，可以管理系统信息、应用设置、环境变量、站点扩展、HTTP 头和服务器变量。 也可以通过浏览到 Function App 的 SCM 终结点（如 `https://<myfunctionapp>.scm.azurewebsites.net/`），启动 Kudu 


### <a name="deployment"></a>Deployment Center

When you use a source control solution to develop and maintain your functions code, Deployment Center lets you build and deploy from source control. Your project is built and deployed to Azure when you make updates. For more information, see [Deployment technologies in Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Cross-origin resource sharing

To prevent malicious code execution on the client, modern browsers block requests from web applications to resources running in a separate domain. [Cross-origin resource sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lets an `Access-Control-Allow-Origin` header declare which origins are allowed to call endpoints on your function app.

#### <a name="portal"></a>门户

When you configure the **Allowed origins** list for your function app, the `Access-Control-Allow-Origin` header is automatically added to all responses from HTTP endpoints in your function app. 

![Configure function app's CORS list](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

When the wildcard (`*`) is used, all other domains are ignored. 

Use the [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) command to add a domain to the allowed origins list. The following example adds the contoso.com domain:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use the [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) command to list the current allowed origins.

### <a name="auth"></a>身份验证

![配置 Function App 的身份验证](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

函数使用 HTTP 触发器时，可以要求首先对调用进行身份验证。 App Service supports Azure Active Directory authentication and sign-in with social providers, such as Facebook, Microsoft, and Twitter. 有关配置特定身份验证提供程序的详细信息，请参阅 [Azure 应用服务身份验证概述](../app-service/overview-authentication-authorization.md)。 


## <a name="next-steps"></a>后续步骤

+ [配置 Azure 应用服务设置](../app-service/configure-common.md)
+ [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 门户]: https://portal.azure.com

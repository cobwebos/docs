---
title: 在 Azure 中配置函数应用设置
description: 了解如何配置 Azure Function App 设置。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 057c030b060343d5bc6f85c38d61feee0b01dfde
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122285"
---
# <a name="manage-your-function-app"></a>管理函数应用 

在 Azure Functions 中，Function App 提供各个函数的执行上下文。 Function App 行为适用于由给定 Function App 托管的所有函数。 函数应用中的所有函数必须使用同一[语言](supported-languages.md)。 

函数应用中的各个函数一起部署并一起缩放。 同一函数应用中的所有函数在函数应用缩放时共享每个实例的资源。 

将为每个函数应用单独定义连接字符串、环境变量以及其他应用程序设置。 必须在函数应用之间共享的任何数据都应该以外部方式存储在持久存储中。

本文介绍如何配置和管理函数应用。 

> [!TIP]  
> 许多配置选项也可通过 [Azure CLI] 进行管理。 

## <a name="get-started-in-the-azure-portal"></a>在 Azure 门户中开始

1. 要开始，请转到 [Azure 门户]，并使用 Azure 帐户登录。 在门户顶部的搜索栏中，输入函数应用的名称，并从列表中选择它。 

2. 在左窗格中的 "**设置**" 下，选择 "**配置**"。

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Azure 门户中 Function App 的概述":::

可以从概述页导航到管理函数应用所需的所有内容，特别是 **[应用程序设置](#settings)** 和 **[平台功能](#platform-features)** 。

## <a name="application-settings"></a><a name="settings"></a>应用程序设置

"**应用程序设置**" 选项卡维护函数应用使用的设置。 这些设置是加密存储的，必须选择“显示值”  才能查看门户中的值。 也可使用 Azure CLI 访问应用程序设置。

### <a name="portal"></a>门户

若要在门户中添加设置，请选择“新建应用程序设置”  并添加新的键值对。

![Azure 门户中的函数应用设置。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) 命令返回现有的应用程序设置，如以下示例所示：

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令添加或更新某个应用程序设置。 以下示例创建的设置包含的键其名称为 `CUSTOM_FUNCTION_APP_SETTING`，其值为 `12345`：


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>使用应用程序设置

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

在本地开发函数应用时，必须将这些值的本地副本保留在 local.settings.json 项目文件中。 若要了解详细信息，请参阅[本地设置文件](functions-run-local.md#local-settings-file)。

## <a name="platform-features"></a>平台功能

函数应用在中运行，并由 Azure App Service 平台维护。 在这种情况下，Function App 有权访问 Azure 核心 Web 托管平台的大多数功能。 在左侧窗格中，可以访问可在函数应用中使用的应用服务平台的许多功能。 

> [!NOTE]
> Function App 运行于消耗托管计划中时，并非所有应用服务功能均可用。

本文的其余部分侧重于 Azure 门户中以下可用于 Functions 的应用服务功能：

+ [应用服务编辑器](#editor)
+ [控制台](#console)
+ [高级工具（Kudu）](#kudu)
+ [部署选项](#deployment)
+ [CORS](#cors)
+ [身份验证](#auth)

若要深入了解如何使用应用服务设置，请参阅[配置 Azure 应用服务设置](../app-service/configure-common.md)。

### <a name="app-service-editor"></a><a name="editor"></a>应用服务编辑器

![应用服务编辑器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

应用服务编辑器是一种高级的门户内编辑器，可用于修改诸如 JSON 配置文件和代码文件等内容。 选择此选项会启动单独的浏览器选项卡和基本编辑器。 借此，可与 Git 存储库集成、运行和调试代码，并可修改 Function App 设置。 同内置的函数编辑器相比，此编辑器为 Functions 提供了增强的开发环境。  

建议你考虑在本地计算机上开发函数。 在本地开发项目并将其发布到 Azure 时，项目文件在门户中处于只读状态。 若要了解详细信息，请参阅[在本地对 Azure Functions 进行编码和测试](functions-develop-local.md)。

### <a name="console"></a><a name="console"></a>控制台

![Function App 控制台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

要从命令行与 Function App 交互时，门户内控制台就是非常合适的开发人员工具。 常见命令包括创建和导航目录与文件，以及执行批处理文件和脚本。 

在本地进行开发时，建议使用 [Azure Functions Core Tools](functions-run-local.md) 和 [Azure CLI]。

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>高级工具 (Kudu)

![配置 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

应用服务的高级工具（也称为 Kudu）提供对 Function App 高级管理功能的访问。 从 Kudu 中，可以管理系统信息、应用设置、环境变量、站点扩展、HTTP 头和服务器变量。 也可以通过浏览到 Function App 的 SCM 终结点（如 `https://<myfunctionapp>.scm.azurewebsites.net/`），启动 Kudu**** 


### <a name="deployment-center"></a><a name="deployment"></a>部署中心

使用源代码管理解决方案来开发和维护函数代码时，可以使用部署中心通过源代码管理进行生成和部署。 进行更新时，会生成项目并将其部署到 Azure。 有关详细信息，请参阅 [Azure Functions 中的部署技术](functions-deployment-technologies.md)。

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>跨域资源共享

为了防止在客户端执行恶意代码，新式的浏览器会阻止 Web 应用程序向正在单独的域中运行的资源发送请求。 [跨域资源共享 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) 允许 `Access-Control-Allow-Origin` 标头声明允许哪些域调用函数应用上的终结点。

#### <a name="portal"></a>门户

配置函数应用的“允许的域”列表时，****`Access-Control-Allow-Origin` 标头会自动添加到函数应用中 HTTP 终结点发出的所有响应。 

![配置函数应用的 CORS 列表](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

使用星号 (`*`) 时，会忽略所有其他的域。 

使用 [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) 命令将域添加到 "允许的来源" 列表中。 以下示例添加 contoso.com 域：

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

使用 [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) 命令列出当前允许的来源。

### <a name="authentication"></a><a name="auth"></a>身份验证

![配置 Function App 的身份验证](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

函数使用 HTTP 触发器时，可以要求首先对调用进行身份验证。 应用服务支持通过社交提供商（例如 Facebook、Microsoft 和 Twitter）进行 Azure Active Directory 身份验证和登录。 有关配置特定身份验证提供程序的详细信息，请参阅 [Azure 应用服务身份验证概述](../app-service/overview-authentication-authorization.md)。 


## <a name="next-steps"></a>后续步骤

+ [配置 Azure 应用服务设置](../app-service/configure-common.md)
+ [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 门户]: https://portal.azure.com

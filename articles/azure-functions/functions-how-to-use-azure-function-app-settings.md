---
title: "配置 Azure Function App 设置 | Microsoft Docs"
description: "了解如何配置 Azure Function App 设置。"
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b7334f68e4526b716210b28388afcd0ee6e48940
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-configure-azure-function-app-settings"></a>如何配置 Azure Function App 设置
## <a name="settings-overview"></a>设置概述
可在门户左下角单击“Function App 设置”链接来管理 Azure Function App 设置。 Azure Function App 设置适用于应用中的所有函数。

1. 转到 [Azure 门户](http://portal.azure.com)，然后使用你的 Azure 帐户登录。
2. 在门户左下角单击“Function App 设置”。 此操作将显示多个可供选择的配置选项。 

![Azure Function App 设置](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="develop"></a>开发
### <a name="app-service-editor"></a>应用服务编辑器
应用服务编辑器是一种高级的门户内编辑器，可用于修改诸如 JSON 配置文件和代码文件等内容。 选择此选项会启动单独的浏览器选项卡和基本编辑器。 借此，可与 GitHub 集成、运行和调试代码，并可修改 Function App 设置。

![应用服务编辑器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="application-settings"></a>应用程序设置
管理环境变量、Framework 版本，远程调试、应用设置、连接字符串、默认文档等。这些设置特定于 Function App。 

若要配置应用设置，请单击“配置应用设置”链接。 

![配置应用设置](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="dev-console"></a>开发人员控制台
可以使用 Azure 函数门户内控制台执行 DOS 样式命令。 常见命令包括创建和导航目录与文件，以及执行批处理文件和脚本。 

> [!NOTE]
> 可以上传脚本，但首先必须在 Azure 函数的“高级设置”中配置 FTP 客户端 。
> 
> 

若要打开门户内控制台，请单击“打开开发人员控制台”。

![配置函数应用内存大小](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> 在内附 ASCII 图文的控制台中工作，可增添酷炫感。
> 
> 

## <a name="deploy"></a>部署
### <a name="continuous-integration"></a>持续集成
可将函数应用与 GitHub、Visual Studio Team Services 等集成。

1. 单击“配置持续集成”链接。 这将打开包含选项的“部署”窗格。
2. 请在“部署”窗格中单击“安装”以显示“部署源”窗格，它包含一个选项：单击“选择源”以显示可用源。 
3. 选择任何可用的部署源：通过单击选择 Visual Studio Team Services、OneDrive、本地 Git 存储库、GitHub、Bitbucket、DropBox 或外部存储库。 
   
    ![配置函数应用的 CI](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. 按不同部署源的提示，输入凭据和信息。 所需凭据和信息可能因选中的源而略有不同。 

设置 CI 后，推送到到已配置源的连接代码会自动部署到此函数应用。

### <a name="kudu"></a>Kudu
通过 Kudu 可访问 Function App 的高级管理功能。

若要打开 Kudu，请单击“转到 Kudu”。 此操作将打开具有 Kudu Web 管理的全新浏览器窗口。

> [!NOTE]
> 或者在函数 URL 中插入“scm”来启动 **Kudu**，如下所示：```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

从 Kudu 网页中，可以查看和管理系统信息、应用设置、环境变量、HTTP 标头、服务器变量等信息。

![配置 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="manage-app-service-settings"></a>管理：应用服务设置
如同管理任何其他 App Service 实例一样管理你的 Function App。 通过此选项可访问上述所有设置和其他几项设置。  

若要打开高级设置，请单击“高级设置”链接。 

![配置应用服务设置](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

有关如何配置应用服务设置的详细信息，请参阅 [配置 Azure 应用服务设置](../app-service-web/web-sites-configure.md)。

## <a name="manage-cors"></a>管理：CORS
通常情况下，出于安全考虑，不允许从外部源对主机（域）调用（例如通过浏览器的 Ajax 调用）。 否则会发送并在后端上执行恶意代码。 最安全的路由是将所有代码源列入阻止列表（信任的代码除外）。 可以通过配置跨域资源共享 (CORS) 来配置接受来自 Azure 函数调用的源。 通过 CORS，可列出特定域，这些域是可在 Azure Function App 中调用函数的 JavaScript 源。 

1. 若要配置 CORS，请单击“配置 CORS”链接。 
2. 输入要列入允许列表的域。

![配置函数应用的 CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="manage-authenticationauthorization"></a>管理：身份验证/授权
对于使用 HTTP 触发器的函数，可以要求对调用进行身份验证。

1. 若要配置身份验证，请单击“配置身份验证”链接。
2. 将“应用服务身份验证”按钮切换为“打开”。

![配置函数应用的 CI](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

大多数身份验证提供程序均需 API 密钥/客户端 ID 和密码；但 Microsoft 帐户和 Facebook 选项还允许用户定义作用域（特定的授权凭据）。 Active Directory 具有可以设置的几个快速或高级配置设置。

有关配置特定身份验证提供程序的详细信息，请参阅 [Azure 应用服务身份验证概述](../app-service/app-service-authentication-overview.md)。

## <a name="manage-api-definition"></a>管理：API 定义
允许客户更轻松地使用 HTTP 触发的函数。

1. 若要设置 API，请单击“配置 API 元数据”。 
2. 输入指向 Swagger json 文件的 URL。

![配置函数应用的 API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

有关使用 Swagger 创建 API 定义的详细信息，请访问 [开始使用 Azure 中的 API 应用、ASP.NET 和 Swagger](../app-service-api/app-service-api-dotnet-get-started.md)。

## <a name="daily-usage-quota"></a>每日使用配额

使用 Azure Functions，可通过设置每日支出配额预见性地限制平台使用情况。 一旦达到每日支出配额，Function App 就会停止运行。 可在设置每日支出配额的同一上下文中重新启用因达到支出配额而停止的 Function App。 支出配额的单位为计费单位：GB-s（GB/秒）；有关计费模型的详细信息，请参阅 [Azure Functions 定价页](http://azure.microsoft.com/pricing/details/functions/)。 

![配置函数应用内存大小](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-quota.png)

## <a name="next-steps"></a>后续步骤
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



---
title: "将 Azure 托管的 API 导出到 PowerApps 和 Microsoft Flow | Microsoft Docs"
description: "有关如何将应用服务中托管的 API 向 PowerApps 和 Microsoft Flow 公开的概述"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/06/2017
ms.author: mahender; mblythe
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: efa5a50564d94dbecd4bc7fcb4082b01d16f680d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>将 Azure 托管的 API 导出到 PowerApps 和 Microsoft Flow


            [PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 是用于构建和使用可跨平台连接到数据和工作的自定义商业应用程序的服务。 通过 [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)，可在喜爱的应用和服务之间轻松自动执行工作流和业务流程。 PowerApps 和 Microsoft Flow 都附带了连接到数据源（如 Office 365、Dynamics 365、Salesforce 等）的各种内置连接器。 在某些情况下，应用和流生成器也希望连接到其组织构建的数据源和 API。

同样，希望在组织内较大范围公开其 API 的开发人员可向应用和流生成器提供其 API。 本主题说明如何导出使用 [Azure Functions](../azure-functions/functions-overview.md) 或 [Azure App Service](../app-service/app-service-web-overview.md) 生成的 API。 导出的 API 将成为自定义连接器，它在 PowerApps 和 Microsoft Flow 中的用法与内置连接器类似。

## <a name="create-and-export-an-api-definition"></a>创建并导出 API 定义
导出 API 前，必须使用 OpenAPI 定义（以前称为 [Swagger](http://swagger.io/) 文件）描述 API。 此定义包含关于 API 中哪些操作可用和应如何安排 API 的请求和响应数据的信息。 PowerApps 和 Microsoft Flow 可为任意 OpenAPI 2.0 定义创建自定义连接器。 Azure Functions 和 Azure App Service 具有内置支持，用于创建、托管和管理 OpenAPI 定义。 有关详细信息，请参阅[在 Azure Web 应用中创建 RESTful API](../app-service/app-service-web-tutorial-rest-api.md)。

> [!NOTE]
> 也可以在 PowerApps 和 Microsoft Flow UI 中生成自定义连接器，而不使用 OpenAPI 定义。 有关详细信息，请参阅[注册并使用自定义连接器 (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) 以及[注册并使用自定义连接器 (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/)。

若要导出 API 定义，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 Azure Functions 或应用服务应用程序。

    如果使用的是 Azure Functions，请选择你的函数应用，然后依次选择“平台功能”、“API 定义”。

    ![Azure Functions API 定义](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    如果使用的是 Azure 应用服务，请从设置列表中选择“API 定义”。

    ![应用服务 API 定义](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. “导出到 PowerApps + Microsoft Flow”按钮应当可用（如果没有，必须先创建 OpenAPI 定义）。 单击此按钮将开始导出过程。

    ![“导出到 PowerApps + Microsoft Flow”按钮](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. 选择“导出模式”：

    使用“快速”选项可以从 Azure 门户内部创建自定义连接器。 这要求你登录到 PowerApps 或 Microsoft Flow，并且有权在目标环境中创建连接器。 如果可以满足此要求，则我们建议采用此方法。 如果使用此模式，请遵照下面的[使用快速导出](#express)说明。

    使用“手动”选项可以导出 API 定义，然后可以使用 PowerApps 或 Microsoft Flow 门户导入。 如果 Azure 用户与有权创建连接器的用户是不同的用户，或者需要在另一个租户中创建连接器，则我们建议采用此方法。 如果使用此模式，请遵照下面的[使用手动导出](#manual)说明。

    ![导出模式](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> 自定义连接器使用 API 定义的副本，因此对应用程序及其 API 定义进行更改时，PowerApps 和 Microsoft Flow 不会立即了解这种更改。 如果确实进行了更改，重复导出步骤以用于新版本。

<a name="express"></a>
## <a name="use-express-export"></a>使用快速导出

若要在“快速”模式下完成导出，请执行以下步骤：

1. 确保已登录到希望导出到的 PowerApps 或 Microsoft Flow 租户。 

2. 使用表中指定的设置。

    |设置|说明|
    |--------|------------|
    |**环境**|选择自定义连接器应保存到的环境。 有关详细信息，请参阅[环境概述](https://powerapps.microsoft.com/tutorials/environments-overview/)。|
    |**自定义 API 名称**|输入名称，PowerApps 和 Microsoft Flow 生成器将在其连接器列表中看到。|
    |**准备安全配置**|如果需要，请提供所需的安全配置详细信息来授予用户对你的 API 的访问权限。 此示例演示 API 密钥。 有关详细信息，请参阅下面的[指定身份验证类型](#auth)。|
 
    ![快速导出到 PowerApps 和 Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. 单击 **“确定”**。 现在自定义连接器已生成并添加到了指定的环境中。

有关对 Azure Functions 使用“快速”模式的示例，请参阅[从 PowerApps 调用函数](functions-powerapps-scenario.md)和[从 Microsoft Flow 调用函数](functions-flow-scenario.md)。

<a name="manual"></a>
## <a name="use-manual-export"></a>使用手动导出

若要完成“手动”模式中的导出，请执行以下步骤：

1. 单击“下载”并保存文件，或者单击“复制”按钮并保存 URL。 导入期间将会使用下载文件或 URL。
 
    ![手动导出到 PowerApps 和 Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. 如果 API 定义包含任何安全定义，会在步骤 2 中调用它们。 在导入期间，PowerApps 和 Microsoft Flow 将检测它们并提示安全信息。 收集与每个定义相关的凭据，以供下一部分使用。 有关详细信息，请参阅下面的[指定身份验证类型](#auth)。

    ![手动导出安全](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    此示例演示 OpenAPI 定义中包含的 API 密钥安全定义。

现在已导出 API 定义，将其导入以在 PowerApps 和 Microsoft Flow 中创建自定义连接器。 以下示例使用 PowerApps，但自定义连接器在两项服务间共享，因此只需导入定义一次即可。

若要将 API 定义导入 PowerApps 和 Microsoft Flow，请执行以下步骤：

1. 登录到 [web.powerapps.com](https://web.powerapps.com) 或 [flow.microsoft.com](https://flow.microsoft.com/)。 

2. 单击页面右上角的“设置”按钮（齿轮图标），并选择“自定义连接器”。

    ![自定义连接器](media/app-service-export-api-to-powerapps-and-flow/custom-connectors.png)

3. 单击“创建自定义连接器”。

4. 在“常规”选项卡上，提供 API 名称，并上传 OpenAPI 定义或粘贴到元数据 URL。 单击“上传”，然后单击“继续”。

    ![“常规”选项卡](media/app-service-export-api-to-powerapps-and-flow/tab-general.png)

5. 在“安全”选项卡上，如果系统提示提供身份验证详细信息，请输入适合身份验证类型的值。 单击“继续”。

    ![“安全”选项卡](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    此示例演示 API 密钥身份验证的必填字段。 字段因身份验证类型而异。

6. “定义”选项卡上会自动填充在 OpenAPI 文件中定义的所有操作。 如果所有必需的操作均已定义，可进入下一步。 如果不是这样，可在此处添加和修改操作。

    ![“定义”选项卡](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    此示例包含一个操作，名为 `CalculateCosts`。 元数据都来自 OpenAPI 文件，正如“说明”一样。

7. 在页面顶部单击“创建连接器”。

现可在 PowerApps 和 Microsoft Flow 中连接到该自定义连接器。 有关在 PowerApps 和 Microsoft Flow 门户中创建连接器的详细信息，请参阅[注册自定义连接器 (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) 和[注册自定义连接器 (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)。

<a name="auth"></a>
## <a name="specify-authentication-type"></a>指定身份验证类型

PowerApps 和 Microsoft Flow 支持一系列标识提供程序，它们都可为自定义连接器提供身份验证。 如果 API 需要身份验证，请确保在 OpenAPI 文档中将其作为_安全定义_捕获，如下例所示：

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
在导出期间，请提供可让 PowerApps 和 Microsoft Flow 进行身份验证的配置值。

本部分介绍“快速”模式支持的身份验证类型：API 密钥、Azure Active Directory 和通用 OAuth 2.0。 PowerApps 和 Microsoft Flow 还支持基本身份验证，对于 Dropbox、Facebook 和 SalesForce 等特定服务还支持 OAuth 2.0。

### <a name="api-key"></a>API 密钥
如果使用 API 密钥，当连接器的用户创建连接时，系统会提示他们提供密钥。 请指定一个 API 密钥名称来帮助他们了解需要哪个密钥。 在前面的示例中，我们使用了名称 `API Key (contact meganb@contoso.com)` 告知用户从何处获取 API 密钥相关信息。 对于 Azure Functions，密钥通常是某个主机密钥，其中涵盖函数应用中的多个函数。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
使用 Azure AD 时，需要两次注册 Azure AD 应用程序：一次注册 API 本身，一次注册自定义连接器：

- 若要配置 API 注册，请使用[应用服务身份验证/授权](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md)功能。

- 若要配置连接器注册，请执行[添加 Azure AD 应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)中的步骤。 该注册必须对你的 API 拥有委派访问权限，并且其答复 URL 应为 `https://msmanaged-na.consent.azure-apim.net/redirect`。 

有关详细信息，请参阅 [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) 和 [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/) 的 Azure AD 注册示例。 这些示例都使用 Azure 资源管理器作为 API；请在按步骤操作时替换你的 API。

需要以下配置值：
- **客户端 ID** - 连接器 Azure AD 注册的客户端 ID
- **客户端机密** - 连接器 Azure AD 注册的客户端机密
- **登录 URL** - Azure AD 的基 URL。 在 Azure 中，这通常是 `https://login.windows.net`。
- **租户 ID** - 用于登录的租户的 ID。 这应是“common”，或在其中创建连接器的租户的 ID。
- **资源 URL** - 用于 API 的 Azure AD 注册的资源 URL

> [!IMPORTANT]
> 如果其他人要在执行手动流的过程中将 API 定义导入 PowerApps 和 Microsoft Flow，则你必须向他们提供“连接器注册”的客户端 ID 和客户端机密，以及 API 的资源 URL。 请确保安全地管理这些机密。 **请勿共享 API 本身的安全凭据。**

### <a name="generic-oauth-20"></a>通用 OAuth 2.0
如果使用通用 OAuth 2.0，则可以与任何 OAuth 2.0 提供程序集成。 这样便可以使用本机不支持的自定义提供程序。

需要以下配置值：
- **客户端 ID** - OAuth 2.0 客户端 ID
- **客户端机密** - OAuth 2.0 客户端机密
- **授权 URL** - OAuth 2.0 授权 URL
- **令牌 URL** - OAuth 2.0 令牌 URL
- **刷新 URL** - OAuth 2.0 刷新 URL




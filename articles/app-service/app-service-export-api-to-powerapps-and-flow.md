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
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>将 Azure 托管的 API 导出到 PowerApps 和 Microsoft Flow

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>为 PowerApps 和 Microsoft Flow 创建自定义连接器

[PowerApps](https://powerapps.com) 是用于构建和使用可跨平台连接到你的数据和工作的自定义商业应用程序的服务。 通过 [Microsoft Flow](https://flow.microsoft.com)，可在你喜爱的应用和服务之间轻松自动执行工作流和业务流程。 PowerApps 和 Microsoft Flow 都附带了连接到数据源（如 Office 365、Dynamics 365、Salesforce 等）的各种内置连接器。 但是，用户还需要能够利用其组织构建的数据源和 API。

同样，希望在组织内较大范围公开其 API 的开发人员可能需要向 PowerApps 和 Microsoft Flow 用户提供其 API。 本主题将展示如何将使用 Azure 应用服务或 Azure Functions 构建的 API 向 PowerApps 和 Microsoft Flow 公开。 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)是平台即服务产品/服务，可帮助开发人员快速轻松地构建企业级 Web、移动和 API 应用程序。 [Azure Functions](https://azure.microsoft.com/services/functions/) 是一种基于事件的无服务器计算解决方案，可帮助用户快速创作可响应系统其他部分并根据需要进行扩展的代码。

若要了解有关这些服务的详细信息，请参阅：
- [PowerApps 引导学习](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow 引导学习](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [什么是应用服务？](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [什么是 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>共享 API 定义

通常使用 [OpenAPI 文档](https://www.openapis.org/)（有时也称为“Swagger”文档）来描述 API。 其包含有关可用操作和数据的组织结构的所有信息。 PowerApps 和 Microsoft Flow 可为任意 OpenAPI 2.0 文档创建自定义连接器。 创建自定义连接器后，可通过与内置连接器完全相同的方式使用它，并可将其快速集成到应用程序。

Azure 应用服务和 Azure Functions 具有[内置支持](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)，用于创建、托管和管理 OpenAPI 文档。 若要为 Web、移动、API 或 函数应用创建自定义连接器，需要为 PowerApps 和 Flow 提供定义的副本。

> [!NOTE]
> 因为正在使用 API 定义的副本，PowerApps 和 Microsoft Flow 将不会立即知道对应用程序的更新或重大更改。 如果新版本的 API 可用，则应对新版本重复执行这些步骤。 

若要为 PowerApps 和 Microsoft Flow 提供应用的托管 API 定义，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com)，然后导航到应用服务或 Azure Functions 应用程序。

    如果使用的是 Azure 应用服务，请从设置列表中选择“API 定义”。 
    
    如果使用的是 Azure Functions，请选择你的函数应用，然后依次选择“平台功能”、“API 定义”。 或者，也可以选择打开“API 定义(预览)”选项卡。

2. 如果已提供 API 定义，将看到“导出到 PowerApps 和 Microsoft Flow”按钮。 单击此按钮将开始导出过程。

3. 选择“导出模式”。 这会确定创建连接器时所要遵循的步骤。 应用服务提供两个选项让你为 PowerApps 和 Microsoft Flow 提供 API 定义：

    使用“快速”选项可以从 Azure 门户内部创建自定义连接器。 此选项要求当前登录的用户有权在目标环境中创建连接器。 如果可以满足此要求，则我们建议采用此方法。 如果使用此模式，请遵照下面的[快速导出](#express)说明。

    使用“手动”选项可以导出 API 定义的副本，然后可以使用 PowerApps 或 Microsoft Flow 门户导入该副本。 如果 Azure 用户与有权创建连接器的用户是不同的用户，或者需要在另一个租户中创建连接器，则我们建议采用此方法。 如果使用此模式，请遵照下面的[手动导出和导入](#manual)说明。

<a name="express"></a>
## <a name="express-export"></a>快速导出

本部分将从 Azure 门户内部创建新的自定义连接器。 此操作要求登录到要导出的租户，并且你在目标环境中必须有权创建自定义连接器。

1. 选择要在其中创建连接器的环境。 然后提供自定义连接器的名称。

2. 如果 API 定义包含任何安全定义，将在步骤 2 中调用它们。 如果需要，请提供所需的安全配置详细信息来授予用户对你的 API 的访问权限。 有关详细信息，请参阅下面的[身份验证](#auth)。 

3. 单击“确定”创建自定义连接器。


<a name="manual"></a>
## <a name="manual-export-and-import"></a>手动导出和导入

若要为 Web、移动、API 或 Function App 创建自定义连接器，需要执行以下两个步骤：

1. [从应用服务或 Azure Functions 检索 API 定义](#export)
2. [将 API 定义导入 PowerApps 和 Microsoft Flow](#import)

这两个步骤可能需要由组织中的不同人员执行，因为给定用户可能不具有同时执行这两个操作的权限。 在这种情况下，具有对应用服务或 Azure Functions 应用程序的参与者访问权限的开发人员将需要获取 API 定义（单个 JSON 文件）或其链接。 然后，他们需要向 PowerApps 或 Microsoft Flow 所有者提供该定义。 该所有者可使用元数据来创建自定义连接器。

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>从应用服务或 Azure Functions 检索 API 定义

本部分将导出应用服务 API 的 API 定义，稍后会在 PowerApps 或 Microsoft Flow 门户中使用此定义。

1. 可以选择**下载 API 定义**或**获取链接**。 无论选择什么，都会在下一部分提供结果。 选择以下选项之一，然后按照说明进行操作。
 
2. 如果 API 定义包含任何安全定义，将在步骤 2 中调用它们。 在导入期间，PowerApps 和 Microsoft Flow 将检测它们并提示安全信息。 收集与每个定义相关的凭据，以供下一部分使用。 有关详细信息，请参阅下面的[身份验证](#auth)。 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>将 API 定义导入 PowerApps 和 Microsoft Flow

本部分将使用先前获得的 API 定义在 PowerApps 和 Microsoft Flow 中创建自定义连接器。 自定义连接器在两项服务间共享，因此只需导入定义一次即可。 有关自定义连接器的详细信息，请参阅[在 PowerApps 中注册并使用自定义连接器]以及[在 Microsoft Flow 中注册并使用自定义连接器]。

1. 打开 [PowerApps Web 门户](https://web.powerapps.com)或 [Microsoft Flow Web 门户](https://flow.microsoft.com/)并登录。 

2. 单击页面右上角的“设置”按钮（齿轮图标），然后选择“自定义连接器”。 

3. 单击“创建自定义连接器”。

4. 在“常规”选项卡上，提供 API 名称，然后上传 OpenAPI 定义或粘贴到元数据 URL。 单击“继续”。

4. 在“安全”选项卡上，如果系统提示提供身份验证详细信息，请输入上一部分中获取的值。 若没有提示，请继续下一步。

5. “定义”选项卡上会自动填充在 OpenAPI 文件中定义的所有操作。 如果所有必需的操作均已定义，可进入下一步。 如果不是这样，可在此处添加和修改操作。

6. 单击“创建连接器”。 如果希望测试 API 调用，请转到下一步。

7. 在“测试”选项卡上，创建连接，选择一个操作进行测试，并输入操作所需的任何数据。

8. 单击“测试操作”。


<a name="auth"></a>
## <a name="authentication"></a>身份验证

PowerApps 和 Microsoft Flow 原生支持可用于将自定义连接器用户登录的标识提供者集合。 如果 API 需要身份验证，请确保在 OpenAPI 文档中将其作为_安全定义_捕获。 在导出期间，需要提供可让 PowerApps 和 Microsoft Flow 执行登录操作的配置值。

本部分介绍快速流支持的身份验证类型：API 密钥、Azure Active Directory 和通用 OAuth 2.0。 有关提供程序的完整列表以及每种提供程序所需的凭据，请参阅[在 PowerApps 中注册并使用自定义连接器]以及[在 Microsoft Flow 中注册并使用自定义连接器]。

### <a name="api-key"></a>API 密钥
如果使用此安全方案，当连接器的用户创建连接时，系统会提示他们提供密钥。 你可以提供一个 API 密钥名称来帮助他们了解需要哪个密钥。 对于 Azure Functions，这通常是某个主机密钥，其中涵盖函数应用中的多个函数。

### <a name="azure-active-directory"></a>Azure Active Directory
在配置需要 AAD 登录的自定义连接器时，需要创建两个 AAD 应用程序注册：一个用来为后端 API 建模，另一个用来为 PowerApps 和 Flow 中的连接器建模。

应将 API 配置为使用第一个注册，如果你使用了[应用服务身份验证/授权](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication)功能，则系统已经完成了此配置。

必须使用[添加 AAD 应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)中所述的步骤，手动为连接器创建第二个注册。 该注册需要对你的 API 拥有委派访问权限，并且其答复 URL 应为 `https://msmanaged-na.consent.azure-apim.net/redirect`。 请参阅[此示例](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)了解详细信息，但要将其中的 Azure Resource Manager 替换为你的 API。

需要以下配置值：
- **客户端 ID** - 连接器 AAD 注册的客户端 ID
- **客户端机密** - 连接器 AAD 注册的客户端机密
- **登录 URL** - AAD 的基 URL。 在 Azure 公共版中，这通常是 `https://login.windows.net`。
- **租户 ID** - 用于登录的租户的 ID。 这应是“common”，或在其中创建连接器的租户的 ID。
- **资源 URL** - API 后端 AAD 注册的资源 URL

> [!IMPORTANT]
> 如果其他人要在执行手动流的过程中将 API 定义导入 PowerApps 和 Microsoft Flow，则你需要向他们提供**连接器注册**的客户端 ID 和客户端机密，以及 API 的资源 URL。 请确保安全地管理这些机密。 **请勿共享 API 本身的安全凭据。**

### <a name="generic-oauth-20"></a>通用 OAuth 2.0
借助通用 OAuth 2.0 支持可与任何 OAuth 2.0 提供程序相集成。 这样，便可以引入原生并不支持的任何自定义提供程序。

需要以下配置值：
- **客户端 ID** - OAuth 2.0 客户端 ID
- **客户端机密** - OAuth 2.0 客户端机密
- **授权 URL** - OAuth 2.0 授权 URL
- **令牌 URL** - OAuth 2.0 令牌 URL
- **刷新 URL** - OAuth 2.0 刷新 URL



[在 PowerApps 中注册并使用自定义连接器]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[在 Microsoft Flow 中注册并使用自定义连接器]: https://flow.microsoft.com/documentation/register-custom-api/


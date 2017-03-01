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
ms.date: 02/06/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>将 Azure 托管的 API 导出到 PowerApps 和 Microsoft Flow

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>为 PowerApps 和 Microsoft Flow 创建自定义 API

[Microsoft 商业应用程序平台](https://businessplatform.microsoft.com/)包含可帮助用户提高工作效率的多种产品。 [PowerApps](https://powerapps.com) 是用于构建和使用可跨平台连接到你的数据和工作的自定义商业应用程序的服务。 通过 [Microsoft Flow](https://flow.microsoft.com)，可在你喜爱的应用和服务之间轻松自动执行工作流和业务流程。 PowerApps 和 Microsoft Flow 都附带了连接到数据源（如 Office 365、Dynamics 365、Salesforce 等）的各种内置连接器。 但是，用户还需要能够利用其组织构建的数据源和 API。

同样，希望在组织内较大范围公开其 API 的开发人员可能需要向 PowerApps 和 Microsoft Flow 用户提供其 API。 本主题将展示如何将使用 Azure 应用服务或 Azure Functions 构建的 API 向 PowerApps 和 Microsoft Flow 公开。 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)是平台即服务产品/服务，可帮助开发人员快速轻松地构建企业级 Web、移动和 API 应用程序。 [Azure Functions](https://azure.microsoft.com/services/functions/) 是一种基于事件的无服务器计算解决方案，可帮助用户快速创作可响应系统其他部分并根据需要进行扩展的代码。

若要了解有关这些服务的详细信息，请参阅：
- [PowerApps 引导学习](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow 引导学习](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [什么是应用服务？](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [什么是 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>共享 API 定义

通常使用 [Open API 文档](https://www.openapis.org/)（有时也称为“Swagger”文档）来描述 API。 其包含有关可用操作和数据的组织结构的所有信息。 PowerApps 和 Microsoft Flow 可以为任意 Open API 2.0 文档创建自定义 API。 创建自定义 API 后，可以通过与内置连接器完全相同的方式使用它，并可以快速集成到应用程序中。

Azure 应用服务和 Azure Functions 具有[内置支持](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)，用于创建、托管和管理 Open API 文档。 若要为 Web、移动、API 或 Function App 创建自定义连接器，需要执行以下两个步骤：

1. [从应用服务或 Azure Functions 检索 API 定义](#export)
2. [将 API 定义导入 PowerApps](#import)

这两个步骤可能需要由组织中的不同人员执行，因为给定用户可能不具有同时执行这两个操作的权限。 在这种情况下，具有对应用服务或 Azure Functions 应用程序的参与者访问权限的开发人员将需要获取 API 定义（单个 JSON 文件）或其链接。 然后，他们需要向 PowerApps 或 Microsoft Flow 所有者提供该定义。 该所有者可以使用元数据来创建自定义 API。

> [!NOTE]
> 因为正在使用 API 定义的副本，PowerApps 和 Microsoft Flow 将不会立即知道对应用程序的更新或重大更改。 如果新版本的 API 可用，则应对新版本重复执行这些步骤。 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>从应用服务或 Azure Functions 检索 API 定义

本部分将导出应用服务 API 的 API 定义，稍后会在 PowerApps 中使用此定义。

1. 打开 [Azure 门户](https://portal.azure.com)，然后导航到应用服务或 Azure Functions 应用程序。

    如果使用的是 Azure 应用服务，请从设置列表中选择“API 定义”。 
    
    如果使用的是 Azure Functions，请选择“Function App 设置”，然后选择“配置 API 元数据”。

2. 如果已提供 API 定义，将看到“导出到 PowerApps 和 Microsoft Flow”按钮。 单击此按钮将开始导出过程。

3. 可以选择**下载 API 定义**或**获取链接**。 无论选择什么，都会在下一部分向 PowerApps 提供结果。 选择以下选项之一，然后按照说明进行操作。
 
4. 如果 API 定义包含任何安全定义，将在步骤 2 中调用它们。 在导入期间，PowerApps 和 Microsoft Flow 将检测它们并提示安全信息。 此服务通过它让用户登录，以便访问 API。 如果你的 API 需要身份验证，请确保在 Open API 文档中将其作为_安全定义_捕获。

    收集与每个定义相关的凭据，以供下一部分使用。 有关 PowerApps 本机支持的标识提供者以及每个标识提供者所需的凭据的列表，请参阅[在 PowerApps 中注册自定义 API] 和[在 Microsoft Flow 中注册自定义 API]。
 
> [!NOTE]
> 如果使用的是 Azure Active Directory 身份验证，将需要一个新的 AAD 应用注册，其已授权访问你的 API 和回复 URL _https://msmanaged-na.consent.azure-apim.net/redirect_。 有关详细信息，请参阅[此示例](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)，将 API 替换为 Azure Resource Manager。
>
> 如果其他用户要将 API 定义导入 PowerApps，那么除 API 定义文件之外，你还将提供**新注册**的客户端 ID 和客户端密钥以及 API 的资源 URL。 请确保安全地管理这些机密。 **请勿共享 API 本身的安全凭据。**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>将 API 定义导入 PowerApps 和 Microsoft Flow

本部分将使用先前获得的 API 定义在 PowerApps 和 Microsoft Flow 中创建自定义 API。 自定义 API 在两项服务之间共享，因此只需导入定义一次即可。 有关自定义 API 的详细信息，请参阅[在 PowerApps 中注册自定义 API] 和[在 Microsoft Flow 中注册自定义 API]。

**导入 PowerApps：**

1. 打开 [PowerApps Web 门户](https://web.powerapps.com)、登录，然后选择“连接”。 单击“新建连接”。

2. 选择“自定义”，然后单击“新建自定义 API”。

3. 提供 API 名称，然后上传 Swagger 定义或粘贴到元数据 URL。 单击“资源组名称” 的 Azure 数据工厂。

4. 如果系统提示你提供身份验证详细信息，请输入上一部分中获取的值。 若没有提示，请继续下一步。

5. 单击“创建” 。

**导入 Microsoft Flow：**

1. 打开 [Microsoft Flow Web 门户](https://flow.microsoft.com/)并登录。 

2. 单击页面右上角的“设置”按钮（齿轮状），然后选择“自定义 API”。 单击“创建自定义 API”。

3. 上传 Swagger 定义，然后单击“继续”。

4. 如果系统提示你提供身份验证详细信息，请输入上一部分中获取的值。 若没有提示，请继续下一步。

5. 单击屏幕顶部的复选框。



[在 PowerApps 中注册自定义 API]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[在 Microsoft Flow 中注册自定义 API]: https://flow.microsoft.com/documentation/register-custom-api/



<!--HONumber=Feb17_HO2-->



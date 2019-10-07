---
title: 在 Azure 时序见解中使用 API 进行身份验证和授权 | Microsoft Docs
description: 本文介绍如何为调用 Azure 时序见解 API 的自定义应用程序配置身份验证和授权。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: e98c004b802711c83558bf4d7ec86c418679836b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981149"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure 时序见解 API 的身份验证和授权

本文档介绍如何使用新的 Azure Active Directory 边栏选项卡在 Azure Active Directory 中注册应用。 在 Azure Active Directory 中注册的应用可让用户进行身份验证，并有权使用与时序见解环境关联的 Azure 时序见解 API。

## <a name="service-principal"></a>服务主体

以下部分介绍如何配置一个应用程序，以代表某个应用访问时序见解 API。 然后，该应用程序可以通过 Azure Active Directory 使用自身的应用程序凭据在时序见解环境中查询或发布参考数据。

## <a name="summary-and-best-practices"></a>摘要和最佳做法

Azure Active Directory 应用注册流程涉及三个主要步骤。

1. 在 Azure Active Directory 中[注册应用程序](#azure-active-directory-app-registration)。
1. 授权应用程序[对时序见解环境进行数据访问](#granting-data-access)。
1. 使用**应用程序 ID** 和**客户端机密**从[客户端应用](#client-app-initialization)中的 `https://api.timeseries.azure.com/` 获取令牌。 然后可使用该令牌调用时序见解 API。

根据**步骤 3**，将应用程序凭据和用户凭据隔离可以：

* 将权限分配给应用标识，这些权限不同于你自己的权限。 通常情况下，这些权限仅限于应用需要的权限。 例如，可仅允许应用读取特定时序见解环境中的数据。
* 使用**客户端机密**或安全证书，将应用安全性与创建用户身份验证凭据的过程相隔离。 因此，应用程序的凭据不依赖于特定用户的凭据。 如果用户的角色发生更改，应用程序不一定需要新的凭据或进一步的配置。 如果用户更改了其密码，对应用程序的所有访问都不需要新的凭据或密钥。
* 使用**客户端机密**或安全证书而不是特定用户的凭据（这需要出示这些凭据）来运行无人参与的脚本。
* 使用安全证书而不是密码来保护对 Azure 时序见解 API 的访问。

> [!IMPORTANT]
> 配置 Azure 时序见解安全策略时，请遵循“关注点分离”的原则（在上述方案中已介绍）。

> [!NOTE]
> * 文中重点介绍了单租户应用程序，其中应用程序只应在一个组织内运行。
> * 通常你会将单租户应用程序作为在组织中运行的业务线应用程序使用。

## <a name="detailed-setup"></a>详细设置

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 应用注册

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授予数据访问权限

1. 对于时序见解环境，请选择“数据访问策略”，然后选择“添加”。

   [![将新的数据访问策略添加到时序见解环境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在“选择用户”对话框中，粘贴 Azure Active Directory 应用注册部分显示的“应用程序名称”或“应用程序 ID”。

   [![在“选择用户”对话框中查找应用程序](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 选择角色。 选择“读取者”以查询数据，或选择“参与者”以查询数据和更改参考数据。 选择“确定”。

   [![在“选择用户角色”对话框中选择“读取者”或“参与者”](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 选择“确定”以保存策略。

   > [!TIP]
   > 了解如何在 Azure Active Directory 中授予对时序见解环境的[数据访问权限](./time-series-insights-data-access.md)。

### <a name="client-app-initialization"></a>客户端应用初始化

1. 使用 Azure Active Directory 应用注册部分显示的“应用程序 ID”和“客户端机密”（应用程序密钥）来代表应用程序获取令牌。

    在 C# 中，以下代码可以代表应用程序获取令牌。 有关完整示例，请参阅[使用 C# 查询数据](time-series-insights-query-data-csharp.md)。

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. 随后可在应用程序调用时序见解 API 时，将令牌传入 `Authorization` 标头。

## <a name="common-headers-and-parameters"></a>常用标头和参数

本部分介绍常见的 HTTP 请求标头和参数，用于针对时序见解 GA 和预览 Api 进行查询。 [时序见解 REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/)中更详细地介绍了特定于 API 的要求。

### <a name="authentication"></a>身份验证

若要对[时序见解 REST api](https://docs.microsoft.com/rest/api/time-series-insights/)执行经过身份验证的查询，必须使用所选的 REST 客户端（Postman、JavaScript、 C#）在[授权标头](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate)中传递有效的 OAuth 2.0 持有者令牌。 

> [!IMPORTANT]
> 令牌必须严格颁发给 @no__t 0 资源（也称为令牌的 "受众"）。
> * 因此， [Postman](https://www.getpostman.com/) **AuthURL**符合： `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> 请参阅托管的 Azure 时序见解[客户端 SDK 示例可视化](https://tsiclientsample.azurewebsites.net/)，了解如何使用[JAVASCRIPT 客户端 SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)以编程方式在图表和图形上使用时序见解 api 进行身份验证。

### <a name="http-headers"></a>HTTP 标头

所需请求标头：

- `Authorization` 用于身份验证和授权，必须在授权标头中传递有效的 OAuth 2.0 持有者令牌。 令牌必须严格颁发给 @no__t 0 资源（也称为令牌的 "受众"）。

可选请求标头：

- `Content-type`-仅支持 `application/json`。
- `x-ms-client-request-id`-客户端请求 ID。 服务记录此值。 允许服务跨服务跟踪操作。
- `x-ms-client-session-id`-客户端会话 ID。 服务记录此值。 允许服务跟踪跨服务的一组相关操作。
- `x-ms-client-application-name`-生成此请求的应用程序的名称。 服务记录此值。

响应标头：

- `Content-type`-仅支持 `application/json`。
- `x-ms-request-id`-服务器生成的请求 ID。 可用于与 Microsoft 联系以调查请求。

### <a name="http-parameters"></a>HTTP 参数

必需的 URL 查询字符串参数：

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

可选 URL 查询字符串参数：

- `timeout=<timeout>` –请求执行的服务器端超时。 仅适用于[获取环境事件](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)和[获取环境聚合](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)api。 超时值应采用 ISO 8601 duration 格式，例如 `"PT20S"`，并且应在 `1-30 s` 的范围内。 默认值为 `30 s`。

## <a name="next-steps"></a>后续步骤

- 有关调用正式版时序见解 API 的示例代码，请参阅[使用 C# 查询数据](./time-series-insights-query-data-csharp.md)。

- 有关预览版时序见解 API 代码示例，请参阅[使用 C# 查询预览版数据](./time-series-insights-update-query-data-csharp.md)。

- 有关 API 参考信息，请参阅[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 了解如何[创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。
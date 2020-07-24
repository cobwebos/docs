---
title: API 身份验证和授权 - Azure 时序见解 | Microsoft Docs
description: 本文介绍如何为调用 Azure 时序见解 API 的自定义应用程序配置身份验证和授权。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 3441a540cb00dc9b85de54543484b1723fa4226e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080739"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure 时序见解 API 的身份验证和授权

本文档介绍如何使用新的 Azure Active Directory 边栏选项卡在 Azure Active Directory 中注册应用。 在 Azure Active Directory 中注册的应用使用户能够使用 Azure 时序见解 API 与 Azure 时序见解环境进行身份验证和授权。

## <a name="service-principal"></a>服务主体

以下部分介绍如何将应用程序配置为代表应用程序访问 Azure 时序见解 API。 然后，应用程序可以通过 Azure Active Directory 使用其自己的应用程序凭据在 Azure 时序见解环境中查询或发布引用数据。

## <a name="summary-and-best-practices"></a>摘要和最佳做法

Azure Active Directory 应用注册流程涉及三个主要步骤。

1. 在 Azure Active Directory 中[注册应用程序](#azure-active-directory-app-registration)。
1. 授权应用程序使用[Azure 时序见解环境的数据访问权限](#granting-data-access)。
1. 使用应用程序 ID 和客户端密码从[客户端应用](#client-app-initialization)中的 `https://api.timeseries.azure.com/` 获取令牌。 然后，可以使用该令牌调用 Azure 时序见解 API。

根据步骤 3，将应用程序凭据和用户凭据隔离可以：

* 将权限分配给应用标识，这些权限不同于你自己的权限。 通常情况下，这些权限仅限于应用需要的权限。 例如，可以允许应用仅从特定的 Azure 时序见解环境中读取数据。
* 使用客户端密码或安全证书，将应用安全性与创建用户身份验证凭据的过程相隔离。 因此，应用程序的凭据不依赖于特定用户的凭据。 如果用户的角色发生更改，应用程序不一定需要新的凭据或进一步的配置。 如果用户更改了其密码，对应用程序的所有访问都不需要新的凭据或密钥。
* 使用客户端密码或安全证书而不是特定用户的凭据（这需要出示这些凭据）来运行无人参与的脚本。
* 使用安全证书而不是密码来保护对 Azure 时序见解 API 的访问。

> [!IMPORTANT]
> 配置 Azure 时序见解安全策略时，请遵循“关注点分离”的原则（在上述方案中已介绍）。

> [!NOTE]
> * 文中重点介绍了单租户应用程序，其中应用程序只应在一个组织内运行。
> * 通常会将单租户应用程序作为在组织中运行的业务线应用程序使用。

## <a name="detailed-setup"></a>详细设置

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 应用注册

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授予数据访问权限

1. 对于 Azure 时序见解环境，请选择 "**数据访问策略**"，然后选择 "**添加**"。

   [![向 Azure 时序见解环境添加新的数据访问策略](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在“选择用户”对话框中，粘贴来自 Azure Active Directory 应用注册部分的“应用程序名称”或“应用程序 ID” 。

   [![在“选择用户”对话框中查找应用程序](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 选择角色。 选择“读取者”以查询数据，或选择“参与者”以查询数据和更改参考数据。 选择“确定”。

   [![在“选择角色”对话框中选择“读取者”或“参与者”](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 通过选择“确定”来保存策略。

   > [!TIP]
   > 对于高级数据访问选项，请阅读[授予数据访问权限](./time-series-insights-data-access.md)。

### <a name="client-app-initialization"></a>客户端应用初始化

* 开发人员可以使用 [Microsoft 身份验证库（MSAL）对 Azure 时序见解进行身份验证。

* 使用 MSAL 进行身份验证：

   1. 使用来自 Azure Active Directory 应用注册部分的“应用程序 ID”和“客户端密码”（应用程序密钥）来代表应用程序获取令牌。

   1. 在 C# 中，以下代码可以代表应用程序获取令牌。 有关如何从 Gen1 环境查询数据的完整示例，请参阅[使用 c # 的查询数据](time-series-insights-query-data-csharp.md)。

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. `Authorization`当应用程序调用 Azure 时序见解 API 时，可以在标头中传递令牌。

> [!IMPORTANT]
> 如果使用[Azure Active Directory 身份验证库（ADAL）](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) ，请阅读有关[迁移到 MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration)的信息。

## <a name="common-headers-and-parameters"></a>常见标头和参数

本部分介绍常见的 HTTP 请求标头和参数，它们用于对 Azure 时序见解 Gen1 和 Gen2 Api 进行查询。 [Azure 时序见解 REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/)中更详细地介绍了特定于 API 的要求。

> [!TIP]
> 阅读 [Azure REST API 参考](https://docs.microsoft.com/rest/api/azure/)，了解有关如何使用 REST API、发出 HTTP 请求和处理 HTTP 响应的详细信息。

### <a name="authentication"></a>身份验证

若要对[Azure 时序见解 REST api](https://docs.microsoft.com/rest/api/time-series-insights/)执行经过身份验证的查询，必须使用所选的 REST 客户端（Postman、JavaScript、c #）在[授权标头](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)中传递有效的 OAuth 2.0 持有者令牌。

> [!TIP]
> 若要了解如何使用[JavaScript 客户端 sdk](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)以编程方式与图表和图形一起使用 Azure 时序见解 api 进行身份验证，请参阅托管的 Azure 时序见解[客户端 SDK 示例可视化](https://tsiclientsample.azurewebsites.net/)。

### <a name="http-headers"></a>HTTP 标头

必需的请求标头如下所述。

| 必需的请求标头 | 描述 |
| --- | --- |
| 授权 | 若要使用 Azure 时序见解进行身份验证，必须在**授权**标头中传递有效的 OAuth 2.0 持有者令牌。 |

> [!IMPORTANT]
> 令牌必须严格颁发给 `https://api.timeseries.azure.com/` 资源（也称为令牌的“受众”）。
> * 因此，[Postman](https://www.getpostman.com/) **AuthURL** 将为：`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com/.default`
> * `https://api.timeseries.azure.com/` 有效，但 `https://api.timeseries.azure.com` 无效。

可选请求标头如下所述。

| 可选请求标头 | 说明 |
| --- | --- |
| Content-type | 仅支持 `application/json`。 |
| x-ms-client-request-id | 客户端请求 ID。 服务记录此值。 允许服务跨服务跟踪操作。 |
| x-ms-client-session-id | 客户端会话 ID。 服务记录此值。 允许服务跨服务跟踪一组相关操作。 |
| x-ms-client-application-name | 生成此请求的应用程序的名称。 服务记录此值。 |

可选但建议的响应标头如下所述。

| 响应标头 | 说明 |
| --- | --- |
| Content-type | 仅支持 `application/json`。 |
| x-ms-request-id | 服务器生成的请求 ID。 可用于与 Microsoft 联系以调查请求。 |
| x-ms-property-not-found-behavior | GA API 可选响应标头。 可能的值为 `ThrowError`（默认）或 `UseNull`。 |

### <a name="http-parameters"></a>HTTP 参数

> [!TIP]
> 如需查找有关必需和可选查询信息的详细信息，请参阅[参考文档](https://docs.microsoft.com/rest/api/time-series-insights/)。

必需的 URL 查询字符串参数依赖于 API 版本。

| 发布 | 可能的 API 版本值 |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` 和 `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> `api-version=2018-11-01-preview`版本即将弃用。 建议用户切换到较新的版本。

可选 URL 查询字符串参数包括为 HTTP 请求执行时间设置超时。

| 可选查询参数 | 说明 | 版本 |
| --- |  --- | --- |
| `timeout=<timeout>` | 用于执行 HTTP 请求的服务器端超时。 仅适用于[获取环境事件](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)和[获取环境聚合](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API。 超时值应采用 ISO 8601 持续时间格式（例如 `"PT20S"`），并且应在 `1-30 s` 范围内。 默认值为 `30 s`。 | Gen1 |
| `storeType=<storeType>` | 对于启用了热存储的 Gen2 环境，可以在或上执行查询 `WarmStore` `ColdStore` 。 查询中的此参数定义应对哪个存储执行查询。 如果未定义，将对 Cold 存储区执行查询。 若要查询 Warm 存储，需要将 storeType 设置为 `WarmStore`。 如果未定义，将对 Cold 存储区执行查询。 | Gen2 |

## <a name="next-steps"></a>后续步骤

* 有关调用 Gen1 Azure 时序见解 API 的示例代码，请参阅[使用 c # 查询 Gen1 数据](./time-series-insights-query-data-csharp.md)。

* 有关调用 Gen2 Azure 时序见解 API 代码示例的示例代码，请参阅[使用 c # 查询 Gen2 数据](./time-series-insights-update-query-data-csharp.md)。

* 有关 API 参考信息，请阅读[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)文档。

* 了解如何[创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

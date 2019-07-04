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
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543909"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure 时序见解 API 的身份验证和授权

本文档介绍如何在 Azure Active Directory 使用新的 Azure Active Directory 边栏选项卡中注册应用。 在 Azure Active Directory 中注册的应用使用户能够进行身份验证并授权使用 Azure 时序见解 API 与时序见解环境相关联。

## <a name="service-principal"></a>服务主体

以下部分介绍如何配置应用程序代表的应用程序访问时序见解 API。 应用程序可能然后查询或使用其自己的应用程序凭据通过 Azure Active Directory 在时序见解环境中发布引用数据。

## <a name="summary-and-best-practices"></a>摘要和最佳实践

Azure Active Directory 应用注册流涉及三个主要步骤。

1. [注册应用程序](#azure-active-directory-app-registration)Azure Active Directory 中。
1. 授权应用程序能够[数据访问时序见解环境](#granting-data-access)。
1. 使用**应用程序 ID**并**客户端机密**若要从一个令牌`https://api.timeseries.azure.com/`中你[客户端应用](#client-app-initialization)。 然后可使用该令牌调用时序见解 API。

每个**第 3 步**，分离应用程序和用户凭据，您可以：

* 将权限分配给应用标识，不同于你自己的权限。 通常情况下，这些权限仅限于应用需要的权限。 例如，可以允许此应用只能从特定的时序见解环境中读取数据。
* 使用找出应用程序的安全保护，防止创建用户的身份验证凭据**客户端机密**或安全证书。 因此，应用程序的凭据不依赖于特定用户的凭据。 如果更改了用户的角色，该应用程序不一定需要新的凭据或进一步配置。 如果用户更改其密码，新的凭据或密钥不需要对应用程序的所有访问。
* 运行无人参与的脚本使用**客户端机密**或安全证书而不是特定用户的凭据 （需要它们存在）。
* 使用安全证书，而不是密码来保护对你 Azure 时序见解 API 的访问。

> [!IMPORTANT]
> 遵循的原则**关注点分离**（介绍对于此方案中更高版本） 时配置 Azure 时序见解安全策略。

> [!NOTE]
> * 文中重点介绍了单租户应用程序，其中应用程序只应在一个组织内运行。
> * 通常将使用在组织中运行的业务线应用程序的单租户应用程序。

## <a name="detailed-setup"></a>详细设置

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 应用注册

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授予数据访问权限

1. 对于时序见解环境，请选择“数据访问策略”，然后选择“添加”   。

   [![将新的数据访问策略添加到时序见解环境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在中**选择用户**对话框框中，粘贴请**应用程序名称**或**应用程序 ID**从 Azure Active Directory 应用注册部分。

   [![在“选择用户”对话框中查找应用程序](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 选择角色。 选择“读取者”以查询数据，或选择“参与者”以查询数据和更改参考数据。   选择“确定”  。

   [![在“选择用户角色”对话框中选择“读取者”或“参与者”](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 选择“确定”以保存策略。 

   > [!TIP]
   > 了解如何在 Azure Active Directory 中授予对时序见解环境的[数据访问权限](./time-series-insights-data-access.md)。

### <a name="client-app-initialization"></a>客户端应用程序初始化

1. 使用**应用程序 ID**并**客户端机密**（应用程序密钥） 从 Azure Active Directory 应用注册部分以获取代表应用程序的令牌。

    在C#，下面的代码可以获取代表应用程序令牌。 有关完整示例，请参阅[使用 C# 查询数据](time-series-insights-query-data-csharp.md)。

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

## <a name="next-steps"></a>后续步骤

- 调用 GA 时序见解 API 的示例代码，请参阅[查询的数据使用C# ](./time-series-insights-query-data-csharp.md)。

- 预览时序见解 API 的代码示例，请参阅[查询预览数据使用C# ](./time-series-insights-update-query-data-csharp.md)。

- 有关 API 参考信息，请参阅[查询 API 参考](/rest/api/time-series-insights/ga-query-api)。

- 了解如何[创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。
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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 602623d48457498963cb5928081d24c1d1132ad4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935246"
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

## <a name="next-steps"></a>后续步骤

- 有关调用正式版时序见解 API 的示例代码，请参阅[使用 C# 查询数据](./time-series-insights-query-data-csharp.md)。

- 有关预览版时序见解 API 代码示例，请参阅[使用 C# 查询预览版数据](./time-series-insights-update-query-data-csharp.md)。

- 有关 API 参考信息，请参阅[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 了解如何[创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。
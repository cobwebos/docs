---
title: "如何在 Azure 时序见解中使用 API 进行身份验证和授权"
description: "本文介绍如何为调用 Azure 时序见解 API 的自定义应用程序配置身份验证和授权。"
services: time-series-insights
ms.service: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: dd78e1e726029aaceef5aff0e0eed84acac646cf
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure 时序见解 API 的身份验证和授权

本文介绍如何配置调用 Azure 时序见解 API 的自定义应用程序中使用的身份验证和授权。

## <a name="service-principal"></a>服务主体

本节介绍如何将应用程序配置为代表其自身来访问时序见解 API。 然后，该应用程序可借助应用程序凭据而非用户凭据在时序见解环境中查询数据或发布参考数据。

如果应用程序须访问时序见解，则必须设置 Azure Active Directory 应用程序，并在时序见解环境中分配数据访问策略。 与使用用户自己的凭据运行应用相比，此方法更优，原因在于：

* 可将权限分配给应用标识，这些权限不同于你自己的权限。 通常情况下，这些权限仅限于应用需要的权限。 例如，可仅允许应用读取特定时序见解环境中的数据。
* 职责发生变化时，无需更改应用的凭据。
* 执行无人参与的脚本时，可使用证书或应用程序密钥自动进行身份验证。

本文介绍如何通过 Azure 门户执行这些步骤。 文中重点介绍了单租户应用程序，其中应用程序只应在一个组织内运行。 通常会将单租户应用程序作为在组织中运行的业务线应用程序使用。

设置流程包含三个高级步骤：

1. 在 Azure Active Directory 中创建应用程序。
2. 授权此应用程序访问时序见解环境。
3. 使用应用程序 ID 和密钥获取 `"https://api.timeseries.azure.com/"` 受众或资源的令牌。 然后可使用该令牌调用时序见解 API。

以下为详细步骤：

1. 在 Azure 门户中，依次选择 Azure Active Directory > “应用注册” > “新应用程序注册”。

   ![Azure Active Directory 中的新应用程序注册](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. 命名应用程序，选择类型“Web 应用/API”，然后为“登录 URL”选择任意有效 URI，单击“创建”。

   ![在 Azure Active Directory 中创建应用程序](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. 选择新创建的应用程序，并将其应用程序 ID 复制到喜爱的文本编辑器中。

   ![复制该应用程序 ID](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. 选择“密钥”，输入密钥名称，选择到期时间，然后单击“保存”。

   ![选择应用程序密钥](media/authentication-and-authorization/active-directory-application-keys.png)

   ![输入密钥名称和到期时间，然后单击“保存”](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. 将密钥复制到喜爱的文本编辑器中。

   ![复制该应用程序密钥](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. 对于时序见解环境，请选择“数据访问策略”，然后单击“添加”。

   ![将新的数据访问策略添加到时序见解环境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. 将步骤 2 中的应用程序名称或步骤 3 中的应用程序 ID 粘贴到“选择用户”对话框中。

   ![在“选择用户”对话框中查找应用程序](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. 选择角色（针对数据查询，选择“阅读者”，针对数据查询和引用数据更改，选择“参与者”），然后单击“确定”。

   ![在“选择角色”对话框中选择“阅读者”或“参与者”](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. 单击“确定”，保存策略。

10. 使用步骤 3 中的应用程序 ID 和步骤 5 中的应用程序密钥代表应用程序获取令牌。 随后可在应用程序调用时序见解 API 时，将令牌传入 `Authorization` 标头。

    如果正在使用 C#，可使用以下代码代表应用程序获取令牌。 有关完整示例，请参阅[使用 C# 查询数据](time-series-insights-query-data-csharp.md)。

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

在应用程序中使用应用程序 ID 和密钥，通过 Azure 时序见解进行身份验证。 

## <a name="next-steps"></a>后续步骤
- 有关调用时序见解 API 的示例代码，请参阅[使用 C# 查询数据](time-series-insights-query-data-csharp.md)。
- 有关 API 参考信息，请参阅[查询 API 参考](/rest/api/time-series-insights/time-series-insights-reference-queryapi)。

> [!div class="nextstepaction"]
> [创建服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)

---
title: 如何使用用户登录保护单页面应用程序
titleSuffix: Azure Maps
description: 如何配置单个页面应用程序，该应用程序支持 Azure Maps Web SDK Azure AD 单一登录。
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 307f1ea8d98aec8594dd4f666d4e18922122b0f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988317"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>使用用户登录保护单页面应用程序

以下指南适用于在内容服务器上承载的应用程序，或具有最小的 web 服务器依赖项的应用程序。 该应用程序提供受保护的资源，仅保护 Azure AD 的用户。 此方案的目标是使 web 应用程序能够对 Azure AD 进行身份验证，并代表用户调用 Azure Maps REST Api。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中创建应用程序注册

在 Azure AD 中创建 web 应用程序，以便用户登录。 Web 应用程序将用户访问权限委派给 Azure Maps REST Api。

1. 在 "Azure 门户的" Azure 服务 "列表中，选择" **Azure Active Directory**"  >  **应用注册**"  >  **新注册**"。  

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 输入**名称**，选择 "**支持帐户类型**"，提供 "重定向 URI"，它表示 Azure AD 将颁发令牌的 url，它是用于托管地图控件的 url。 有关详细示例，请参阅[Azure Maps Azure AD 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)。 然后选择“注册”。  

3. 若要将委派的 API 权限分配到 Azure Maps，请访问应用程序。 然后，在 "**应用注册**" 下，选择 " **API 权限**" "  >  **添加权限**"。 在 "**我的组织使用的 api**" 下，搜索并选择 " **Azure Maps**"。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

4. 选中 " **Access Azure Maps**旁边的复选框，然后选择"**添加权限**"。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

5. 启用 `oauth2AllowImplicitFlow`。 若要启用它，请在应用注册的**清单**部分中将设置 `oauth2AllowImplicitFlow` 为 `true` 。

6. 从应用注册中复制 Azure AD 应用 ID 和 Azure AD 租户 ID，以便在 Web SDK 中使用。 将 Azure AD 应用注册详细信息，并将 `x-ms-client-id` 从 Azure 映射帐户添加到 WEB SDK。

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. 为用户或组配置基于 Azure 角色的访问控制。 请参阅[以下部分，启用 RBAC](#grant-role-based-access-for-users-to-azure-maps)。
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>后续步骤

进一步了解单页面应用程序方案：
> [!div class="nextstepaction"]
> [单页应用程序](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)

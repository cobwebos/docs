---
title: 如何使用交互式单一登录来保护 web 应用程序
titleSuffix: Azure Maps
description: 如何使用 OpenID Connect 协议配置支持 Azure Maps Web SDK Azure AD 单一登录的 web 应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 1668c7ccad75771a598aaa55f5403f070ea2dff8
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090210"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>使用用户登录来保护 web 应用程序

以下指南适用于在 web 服务器上托管的应用程序，维护多个业务方案，并部署到 web 服务器。 应用程序要求提供仅保护 Azure AD 用户保护的受保护资源。 此方案的目标是使 web 应用程序能够对 Azure AD 进行身份验证，并代表用户调用 Azure Maps REST Api。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中创建应用程序注册

你必须在 Azure AD 中创建 web 应用程序，以便用户登录。 然后，此 web 应用程序会委托用户对 Azure Maps REST Api 的访问权限。

1. 在 "Azure 门户的" Azure 服务 "列表中，选择" **Azure Active Directory**"  >  **应用注册**"  >  **新注册**"。  

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 输入 **名称**，选择 " **支持帐户类型**"，提供 "重定向 URI"，它表示 Azure AD 将颁发令牌的 url，它是用于托管地图控件的 url。 有关更多详细信息，请参阅 Azure AD [方案：登录用户的 Web 应用](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)。 完成 Azure AD 方案中提供的步骤。  

3. 完成应用程序注册后，确认应用程序登录是否适用于用户。 登录完成后，可以向应用程序授予对 Azure Maps REST Api 的委托访问权限。
    
4.  若要将委派的 API 权限分配到 Azure Maps，请访问应用程序。 然后选择 " **API 权限**" "  >  **添加权限**"。 在 " **我的组织使用的 api**" 下，搜索并选择 " **Azure Maps**"。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

5. 选中 " **Access Azure Maps**旁边的复选框，然后选择" **添加权限**"。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

6. 通过使用应用程序密钥配置应用注册，使 web 应用程序能够调用 Azure Maps REST Api，有关详细步骤，请参阅用于 [调用 Web api 的 web 应用：应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-app-registration)。 需要提供机密才能 Azure AD 代表用户进行身份验证。 应用注册证书或机密应存储在安全存储区中，以便 web 应用程序可以检索到 Azure AD 的身份验证。 
   
   * 如果应用程序已配置 Azure AD 应用注册和密钥，则可以跳过此步骤。

> [!Tip]
> 如果应用程序托管在 Azure 环境中，我们建议使用 [azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 和 Azure Key Vault 实例通过 [获取访问令牌](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) 访问 Azure Key Vault 机密或证书来访问机密。 若要连接到 Azure Key Vault 以检索机密，请参阅 [教程，通过托管标识进行连接](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)。
   
7. 为 Azure Maps Web SDK 实现一个安全令牌终结点，以访问令牌。 
   
   * 有关令牌控制器的示例，请参阅 [Azure Maps Azure AD 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)。 
   * 对于非 AspNetCore 实现或其他，请参阅从 Azure AD 文档 [获取应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token) 。
   * 安全令牌终结点负责为经过身份验证和授权的用户返回访问令牌，以调用 Azure Maps REST Api。

8. 为用户或组配置 azure RBAC)  (azure 基于角色的访问控制。 请参阅 [为用户授予基于角色的访问权限](#grant-role-based-access-for-users-to-azure-maps)。

9. 使用 Azure Maps Web SDK 配置 web 应用程序页，以便访问安全令牌终结点。 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>后续步骤

进一步了解 web 应用程序方案：
> [!div class="nextstepaction"]
> [场景：可将用户登录的 Web 应用](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps Azure AD Web 应用示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)

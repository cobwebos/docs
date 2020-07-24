---
title: 如何保护守护程序应用程序
titleSuffix: Azure Maps
description: 使用 Azure 门户管理身份验证以配置受信任的后台应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 066118622f19d7efac71ddd66ac1abe058008b55
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126765"
---
# <a name="secure-a-daemon-application"></a>保护后台应用程序

以下指南适用于受信任且安全的环境中托管的后台进程、计时器和作业。 示例包括 Azure Web 作业、Azure Function App、Windows 服务和任何其他可靠的后台服务。

> [!Tip]
> Microsoft 建议实现适用于生产应用程序的 Azure Active Directory （Azure AD）和基于角色的访问控制（RBAC）。 有关概念的概述，请参阅[Azure Maps Authentication](./azure-maps-authentication.md)。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>方案：共享密钥身份验证

创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 当[使用共享密钥身份验证调用 Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)时，建议使用主密钥作为订阅密钥。 你可以在某些情况下使用辅助密钥，例如滚动密钥更改。 有关详细信息，请参阅[Azure Maps 中的身份验证](https://aka.ms/amauth)。

### <a name="securely-store-shared-key"></a>安全存储共享密钥

主密钥和辅助密钥允许映射帐户的所有 Api 的授权。 应用程序应将密钥存储在安全存储区中，如 Azure Key Vault。 应用程序必须检索共享密钥作为 Azure Key Vault 机密，以避免在应用程序配置中以纯文本格式存储共享密钥。 若要了解如何配置 Azure Key Vault，请参阅[Azure Key Vault 开发人员指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide)。

以下步骤概述了此过程：

1. 创建 Azure 密钥保管库。
2. 创建一个 Azure AD 的服务主体，方法是创建一个应用注册或托管标识，创建的主体负责访问 Azure Key Vault。
3. 将服务主体访问权限分配给 Azure 密钥机密 `Get` 权限。
4. 为开发人员临时分配机密 `Set` 权限的访问权限。
5. 设置 Key Vault 机密中的共享密钥，并将该密钥 ID 作为后台应用程序应用程序的配置进行引用，并删除机密 `Set` 权限。
6. 在后台应用程序中实现 Azure AD 身份验证，以从 Azure Key Vault 检索共享密钥机密。
7. 创建具有共享密钥的 Azure Maps REST API 请求。

> [!Tip]
> 如果应用托管在 Azure 环境中，则应实现托管标识，以降低管理机密以 Azure Key Vault 进行身份验证所需的成本和复杂性。 请参阅以下 Azure Key Vault[教程，通过托管标识进行连接](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)。

后台应用程序负责从安全存储中检索共享密钥。 具有 Azure Key Vault 的实现要求通过 Azure AD 进行身份验证才能访问机密。 相反，我们鼓励直接 Azure AD RBAC 身份验证 Azure Maps，因为使用共享密钥身份验证的其他复杂性和操作要求。

> [!IMPORTANT]
> 为了简化密钥再生，建议应用程序一次使用一个密钥。 然后，应用程序可以重新生成未使用的密钥，并将新的再生密钥部署到受保护的机密存储区，如 Azure Key Vault。

## <a name="scenario-azure-ad-role-based-access-control"></a>方案： Azure AD 基于角色的访问控制

创建 Azure Maps 帐户后，Azure Maps `x-ms-client-id` 值就会出现在 Azure 门户身份验证详细信息页中。 此值表示将用于 REST API 请求的帐户。 应将此值存储在应用程序配置中，并在发出 HTTP 请求之前检索此值。 此方案的目标是使后台应用程序能够通过身份验证 Azure AD 并调用 Azure Maps REST Api。

> [!Tip]
> 建议在 Azure 虚拟机、虚拟机规模集或应用服务上托管，以启用托管标识组件的好处。

### <a name="daemon-hosted-on-azure-resources"></a>托管在 Azure 资源上的后台程序

在 Azure 资源上运行时，配置 Azure 托管标识以实现低成本、最小凭据管理工作量。 

请参阅[托管标识概述](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，使应用程序能够访问托管标识。

托管标识权益：

* Azure 系统托管 X509 证书公钥加密身份验证。
* Azure AD 具有 X509 证书的安全，而不是客户端密码。
* Azure 会管理和续订与托管标识资源关联的所有证书。
* 通过删除任何对受保护的密钥存储服务（如 Azure Key Vault）的需求，简化了凭据操作管理。 

### <a name="daemon-hosted-on-non-azure-resources"></a>非 Azure 资源上托管的守护程序

在非 Azure 环境中运行时，托管标识不可用。 因此，你必须通过后台应用程序的 Azure AD 应用程序注册来配置服务主体。

1. 在 "Azure 门户的" Azure 服务 "列表中，选择" **Azure Active Directory**"  >  **应用注册**"  >  **新注册**"。  

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 如果已经注册了应用，则继续执行下一步。 如果尚未注册应用，请输入**名称**，选择 "**支持帐户类型**"，然后选择 "**注册**"。  

    > [!div class="mx-imgBorder"]
    > ![应用注册详细信息](./media/how-to-manage-authentication/app-create.png)

3. 若要将委派的 API 权限分配到 Azure Maps，请访问应用程序。 然后，在 "**应用注册**" 下，选择 " **API 权限**" "  >  **添加权限**"。 在 "**我的组织使用的 api**" 下，搜索并选择 " **Azure Maps**"。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

4. 选中 " **Access Azure Maps**旁边的复选框，然后选择"**添加权限**"。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

5. 完成以下步骤来创建客户端机密或配置证书。

    * 如果你的应用程序使用服务器或应用程序身份验证，请在你的应用程序注册页上，中转到**证书 & 密码**。 然后，通过选择 "**新建客户端密钥**" 上传公钥证书或创建密码。

        > [!div class="mx-imgBorder"]
        > ![创建客户端机密](./media/how-to-manage-authentication/app-keys.png)

    * 选择 "**添加**" 后，复制密钥并将其安全地存储在服务中，如 Azure Key Vault。 查看[Azure Key Vault 开发人员指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide)，以安全地存储证书或机密。 将使用此机密从 Azure AD 获取令牌。

        > [!div class="mx-imgBorder"]
        > ![添加客户端密码](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>向后台应用程序授予基于角色的访问权限，以便 Azure Maps

可以通过将创建的托管标识或服务主体分配给一个或多个 Azure Maps 访问控制角色定义来授予*基于角色的访问控制*（RBAC）。 若要查看 Azure Maps 可用的 RBAC 角色定义，请访问 "**访问控制（IAM）**"。 选择 "**角色**"，然后搜索以*Azure Maps*开头的角色。 这些 Azure Maps 角色是可以向其授予访问权限的角色。

> [!div class="mx-imgBorder"]
> ![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. 中转到你的**Azure Maps 帐户**。 选择“访问控制(IAM)” > “角色分配”。

    > [!div class="mx-imgBorder"]
    > ![授予 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 "**角色分配**" 选项卡上，**添加**角色分配。 
    
    > [!div class="mx-imgBorder"]
    > ![添加角色分配](./media/how-to-manage-authentication/add-role-assignment.png)

3. 选择内置 Azure Maps 角色定义，如**Azure Maps 数据读取器**或**Azure Maps 数据参与者**。 在 "**分配访问权限**" 下，选择 "使用**用户分配**的托管标识系统**Azure AD 用户、组或服务主体**或托管标识" 分配的托管标识  /  **System assigned Managed identity**。 选择主体。 再选择“保存”。

    > [!div class="mx-imgBorder"]
    > ![添加角色分配](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. 你可以确认已在 "角色分配" 选项卡上应用了角色分配。

## <a name="request-token-with-managed-identity"></a>具有托管标识的请求令牌

为宿主资源配置托管标识后，请使用 Azure SDK 或 REST API 获取用于 Azure Maps 的令牌，请参阅[获取访问令牌](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)中的详细信息。 在本指南中，假定将返回访问令牌，可在 REST API 请求中使用该令牌。

## <a name="request-token-with-application-registration"></a>具有应用程序注册的请求令牌

注册应用并将其与 Azure Maps 相关联后，可以请求访问令牌。

* Azure AD 资源 ID`https://atlas.microsoft.com/`
* Azure AD 应用 ID
* Azure AD 租户 ID
* Azure AD 应用注册客户端密码

请求：

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

响应：

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

有关更多详细示例，请参阅[Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="next-steps"></a>后续步骤

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

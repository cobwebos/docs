---
title: 管理身份验证 |Microsoft Azure 映射
description: 使用 Azure 门户在 Microsoft Azure 映射中管理身份验证。
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 30eb637e9117818758ed4ab0e3adef9db29cc698
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057305"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户后，将创建客户端 ID 和密钥以支持 Azure Active Directory （Azure AD）身份验证和共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 当[使用共享密钥身份验证调用 Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)时，建议使用主密钥作为订阅密钥。 你可以在某些情况下使用辅助密钥，例如滚动密钥更改。 有关详细信息，请参阅[Azure Maps 中的身份验证](https://aka.ms/amauth)。

可以在 Azure 门户中查看身份验证详细信息。 在帐户的 "**设置**" 菜单上，选择 "**身份验证**"。

![身份验证详细信息](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>注册和配置 Azure AD 应用

1. 在 "Azure 门户的" Azure 服务 "列表中，选择" **Azure Active Directory** > **应用注册**" > **新注册**"。  

    ![应用注册](./media/how-to-manage-authentication/app-registration.png)

1. 如果已经注册了应用，则继续执行下一步。 如果尚未注册应用，请输入**名称**，选择 "**支持帐户类型**"，然后选择 "**注册**"。  

    ![应用注册详细信息](./media/how-to-manage-authentication/app-create.png)

1. 若要将委派的 API 权限分配到 Azure Maps，请访问应用程序。 然后，在 "**应用注册**" 下，选择 " **API 权限**" > "**添加权限**"。 在 "**我的组织使用的 api**" 下，搜索并选择 " **Azure Maps**"。

    ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

1. 选中 " **Access Azure Maps**旁边的复选框，然后选择"**添加权限**"。

    ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

1. 根据身份验证方法完成以下步骤之一。 

    * 如果你的应用程序对 Azure Maps Web SDK 使用用户令牌身份验证，则启用 `oauth2AllowImplicitFlow`。 若要启用它，请在应用注册的**清单**部分中将 `oauth2AllowImplicitFlow` 设置为 true。 
    
       ![应用部件清单](./media/how-to-manage-authentication/app-manifest.png)

    * 如果你的应用程序使用服务器或应用程序身份验证，请在你的应用程序注册页上，中转到**证书 & 密码**。 然后，通过选择 "**新建客户端密钥**" 上传公钥证书或创建密码。 
    
       ![创建客户端机密](./media/how-to-manage-authentication/app-keys.png)

        如果创建密码，请在选择 "**添加**" 后，复制并安全地存储密码。 将使用此密码从 Azure AD 获取令牌。

       ![添加客户端密码](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>向 Azure Maps 授予基于角色的访问控制

将 Azure Maps 帐户与 Azure AD 租户关联后，可以授予访问控制权限。 可以通过将用户、组或应用程序分配给一个或多个 Azure Maps 访问控制角色来授予*基于角色的访问控制*（RBAC）。 

1. 中转到你的**Azure Maps 帐户**。 选择 "**访问控制（IAM）** " > **角色分配**。

    ![授予 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. 在 "**角色分配**" 选项卡上的 "**角色**" 下，选择 " **Azure Maps 日期读取者（预览版）** "。 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”。 选择用户或应用程序。 再选择“保存”。

    ![添加角色分配](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>查看可用 Azure Maps RBAC 角色

若要查看可用于 Azure Maps 的 RBAC 角色，请访问**访问控制（IAM）** 。 选择 "**角色**"，然后搜索以*Azure Maps*开头的角色。 这些 Azure Maps 角色是可以向其授予访问权限的角色。

![查看可用角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>查看 Azure Maps RBAC

RBAC 提供粒度访问控制。

若要查看已为 Azure Maps 授予 RBAC 的用户和应用，请访问**访问控制（IAM）** 。 在此处选择 "**角色分配**"，然后按**Azure Maps**进行筛选。

![查看已获 RBAC 的用户和应用](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

注册应用并将其与 Azure Maps 相关联后，可以请求访问令牌。

如果你的应用程序对 Azure Maps Web SDK 使用用户令牌身份验证，则使用 Azure Maps 的客户端 ID 和 Azure AD 应用 ID 配置 HTML 页面。

如果应用程序使用服务器或应用程序身份验证，则 Azure AD 令牌终结点 `https://login.microsoftonline.com`请求令牌。 在请求中，使用以下详细信息： 

* Azure AD 资源 ID `https://atlas.microsoft.com/`
* Azure Maps 客户端 ID
* Azure AD 应用 ID
* Azure AD 应用注册密码或证书

| Azure 环境   | Azure AD 令牌终结点 | Azure 资源 ID |
| --------------------|-------------------------|-------------------|
| Azure 公有云        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 政府云   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

有关请求用户和服务主体 Azure AD 的访问令牌的详细信息，请参阅[Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Azure AD 和 Azure Maps WEB SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"] 
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

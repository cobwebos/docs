---
title: 在 Azure Maps 中管理身份验证 | Microsoft Docs
description: 可以使用 Azure 门户管理 Azure Maps 中的身份验证。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693959"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户后，客户端 ID 和密钥将创建以支持 Azure Active Directory (Azure AD) 或共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

在 Azure 门户中，可以查看你的身份验证详细信息。 转到帐户并选择**身份验证**上**设置**菜单。

![身份验证详细信息](./media/how-to-manage-authentication/how-to-view-auth.png)

 若要了解详细信息，请参阅[身份验证与 Azure Maps](https://aka.ms/amauth)。


## <a name="set-up-azure-ad-app-registration"></a>设置 Azure AD 应用注册

创建 Azure Maps 帐户后，需要建立 Azure AD 租户与 Azure Maps 资源之间的链接。

1. 转到 Azure AD 边栏选项卡，并创建应用注册。 提供注册的名称。 在中**单一登录 URL**框中，提供 web 应用的主页 / API (例如，https:\//localhost/)。 如果你已拥有已注册的应用，请转到步骤 2。

    ![应用注册](./media/how-to-manage-authentication/app-registration.png)

    ![应用程序注册详细信息](./media/how-to-manage-authentication/app-create.png)

2. 要为 Azure Maps API 的委托的权限，请转到下应用程序**应用注册**，然后选择**设置**。  选择**所需的权限**，然后选择**添加**。 搜索并选择**Azure Maps**下**选择 API**，然后选择**选择**按钮。

    ![API 应用权限](./media/how-to-manage-authentication/app-permissions.png)

3. 下**选择权限**，选择**访问 Azure Maps**，然后选择**选择**按钮。

    ![选择 API 应用权限](./media/how-to-manage-authentication/select-app-permissions.png)

4. 完成步骤或 b，具体取决于你的身份验证方法。

    1. 如果你的应用程序使用了 Azure Maps Web SDK 用户令牌身份验证，启用`oauthEnableImplicitFlow`通过您的应用程序注册详细信息页的清单部分中将其设置为 true。
    
       ![应用部件清单](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果你的应用程序使用服务器/应用程序身份验证，请转到**密钥**中应用注册边栏选项卡，并创建密码，或将公钥证书上传到应用注册。 如果您创建密码，在选择后**保存**，供以后复制密码并将其安全地存储。 此密码将用于获取 Azure AD 的令牌。

       ![应用密钥](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>向 Azure Maps 授予 RBAC

你将 Azure Maps 帐户与 Azure AD 租户相关联后，您可以通过将用户或应用程序到一个或多个 Azure Maps 访问控制角色分配授予访问控制。

1. 转到**访问控制 (IAM)**，选择**角色分配**，然后选择**添加角色分配**。

    ![授予 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在中**添加的角色分配**窗口下**角色**，选择**Azure Maps 日期读取器 （预览版）**。 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”。 下**选择**，选择用户或应用程序。 选择“保存”。

    ![添加角色分配](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>查看可用 Azure Maps RBAC 角色

若要查看 Azure 地图提供的基于角色的访问控制 (RBAC) 角色，请转到**访问控制 (IAM)**，选择**角色**，然后搜索角色从和**Azure Maps**. 这些是可以授予访问权限的角色。

![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>查看 Azure Maps RBAC

RBAC 提供精细的访问控制。

若要查看用户和已被授予 Azure 地图的 RBAC 的应用，请转到**访问控制 (IAM)**，选择**角色分配**，然后筛选**Azure Maps**。

![查看用户和应用程序授予 RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

在注册你的应用并与 Azure Maps 之后，你可以请求访问令牌。

* 如果你的应用程序使用了 Azure Maps Web SDK 用户令牌身份验证，则需要配置你的 HTML 页与 Azure Maps 客户端 ID 和 Azure AD 应用程序 id。

* 如果你的应用程序使用服务器/应用程序身份验证，则需要从 Azure AD 登录终结点请求令牌`https://login.microsoftonline.com`与 Azure AD 资源 ID `https://atlas.microsoft.com/`，Azure Maps 客户端 ID、 Azure AD 应用 ID 和 Azure AD 应用注册密码或证书。

有关从用户和服务主体的 Azure AD 请求访问令牌的详细信息，请参阅[Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure AD 身份验证和 Azure Maps Web SDK 的详细信息，请参阅 [Azure AD 和 Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
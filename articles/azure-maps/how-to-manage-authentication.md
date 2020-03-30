---
title: 管理身份验证 |微软 Azure 地图
description: 使用 Azure 门户在 Microsoft Azure 映射中管理身份验证。
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335526"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure 地图帐户后，将创建客户端 ID 和密钥以支持 Azure 活动目录 （Azure AD） 身份验证和共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

创建 Azure 地图帐户后，将生成主键和辅助键。 我们建议您[在使用共享密钥身份验证调用 Azure 映射](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)时使用主密钥作为订阅密钥。 您可以在滚动键更改等方案中使用辅助键。 有关详细信息，请参阅[Azure 映射中的身份验证](https://aka.ms/amauth)。

您可以在 Azure 门户中查看身份验证详细信息。 在您的帐户中，在 **"设置"** 菜单上，选择 **"身份验证**"。

![身份验证详细信息](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>注册和配置 Azure AD 应用

1. 在 Azure 门户中，在 Azure 服务列表中，选择**Azure 活动目录** > **应用注册** > **"新注册**"。  

    ![应用注册](./media/how-to-manage-authentication/app-registration.png)

1. 如果您已注册应用，请继续执行下一步。 如果您尚未注册应用，请输入**名称**，选择**支持帐户类型**，然后选择"**注册**"。  

    ![应用注册详细信息](./media/how-to-manage-authentication/app-create.png)

1. 要将委派的 API 权限分配给 Azure 映射，请转到应用程序。 然后在**应用注册**下，选择**API 权限** > **添加权限**。 在我的**组织使用的 API**下，搜索并选择**Azure 映射**。

    ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

1. 选择 **"访问 Azure 地图**"旁边的复选框，然后选择 **"添加权限**"。

    ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

1. 完成以下步骤之一，具体取决于身份验证方法。 

    * 如果应用程序使用 Azure 映射 Web SDK 的用户令牌身份验证，则`oauth2AllowImplicitFlow`启用 。 要启用它，在应用注册的 **"清单"** 部分中，设置为`oauth2AllowImplicitFlow`true。 
    
       ![应用部件清单](./media/how-to-manage-authentication/app-manifest.png)

    * 如果应用程序使用服务器或应用程序身份验证，则在应用注册页上转到**证书&机密**。 然后上传公钥证书或通过选择 **"新客户端机密**"创建密码。 
    
       ![创建客户端机密](./media/how-to-manage-authentication/app-keys.png)

        如果创建密码，则在选择 **"添加**"后，复制密码并将其安全地存储。 您将使用此密码从 Azure AD 获取令牌。

       ![添加客户端机密](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>向 Azure 映射授予基于角色的访问控制

将 Azure 地图帐户与 Azure AD 租户关联后，可以授予访问控制。 通过将用户、组或应用程序分配给一个或多个 Azure 映射访问控制角色，可以授予*基于角色的访问控制*（RBAC）。 

1. 转到**Azure 地图帐户**。 选择**访问控制 （IAM）** > **角色分配**。

    ![授予 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. 在 **"角色分配**"选项卡上，在 **"角色**"下，选择**Azure 地图日期读取器（预览）。** 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”********。 选择用户或应用程序。 再选择“保存”****。

    ![添加角色分配](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>查看可用 Azure Maps RBAC 角色

要查看可用于 Azure 映射的 RBAC 角色，请转到**访问控件 （IAM）。** 选择**角色**，然后搜索以*Azure 映射*开头的角色。 这些 Azure 映射角色是您可以授予访问权限的角色。

![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>查看 Azure 地图 RBAC

RBAC 提供精细的访问控制。

要查看已授予 Azure 地图 RBAC 的用户和应用，请转到**访问控制 （IAM）。** 在此处，选择**角色分配**，然后按**Azure 映射**进行筛选。

![查看已被授予 RBAC 的用户和应用](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

注册应用并将其与 Azure 映射关联后，可以请求访问令牌。

如果应用程序使用 Azure 映射 Web SDK 的用户令牌身份验证，则使用 Azure 映射客户端 ID 和 Azure AD 应用 ID 配置 HTML 页。

如果应用程序使用服务器或应用程序身份验证，则从 Azure AD 令牌终结点`https://login.microsoftonline.com`请求令牌。 在您的请求中，请使用以下详细信息： 

* Azure AD 资源 ID`https://atlas.microsoft.com/`
* Azure 映射客户端 ID
* Azure AD 应用 ID
* Azure AD 应用注册密码或证书

| Azure 环境   | Azure AD 令牌终结点 | Azure 资源 ID |
| --------------------|-------------------------|-------------------|
| Azure 公共云        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 政府版云   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

有关向用户和服务主体请求 Azure AD 的访问令牌的详细信息，请参阅 Azure [AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Azure AD 和 Azure 映射 Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

查找 Azure 地图帐户的 API 使用情况指标：
> [!div class="nextstepaction"] 
> [查看使用情况指标](how-to-view-api-usage.md)

浏览演示如何将 Azure AD 与 Azure 映射集成的示例：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

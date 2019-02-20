---
title: 在 Azure Maps 中管理身份验证 | Microsoft Docs
description: 可以使用 Azure 门户管理 Azure Maps 中的身份验证。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242679"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户后，将创建客户端 ID 和密钥，用于支持 Azure Active Directory (Azure AD) 或共享密钥身份验证。

可以通过在 Azure 门户中导航到“设置”下的“身份验证”页，查找身份验证详细信息。 导航到你的帐户。 然后，从菜单中选择“身份验证”。


## <a name="view-authentication-details"></a>查看身份验证详细信息

若要查看身份验证详细信息，请导航到设置菜单中的“身份验证”选项。

![查看身份验证](./media/how-to-manage-authentication/how-to-view-auth.png)

 若要了解身份验证和 Azure Maps，请参阅[使用 Azure Maps 进行身份验证](https://aka.ms/amauth)。


## <a name="configure-azure-ad-app-registration"></a>配置 Azure AD 应用注册

创建 Azure Maps 帐户后，需要 Azure AD 租户与 Azure Maps Azure 资源之间的链接。 

1. 转到 Azure AD 边栏选项卡并创建具有名称和登录 URL 的应用注册，作为 Web 应用/API 的主页，例如“https://localhost/”。 如果已拥有已注册应用，请转到步骤 2。

    ![应用注册](./media/how-to-manage-authentication/app-registration.png)

    ![应用注册](./media/how-to-manage-authentication/app-create.png)

2. 通过导航到应用注册下的应用程序，向 Azure Maps 分配委托的 API 权限，然后单击“设置”。  依次选择“所需权限”、“添加”。 搜索，然后选择“选择 API”下的 Azure Maps 并单击“选择”。

    ![应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

3. 最后，在“选择权限”下选择“访问 Azure Maps”并单击“选择”。

    ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

4. 请按照步骤 a 或 b 操作，具体取决于身份验证实现。

    1. 如果应用程序想要对 Azure Maps Web SDK 使用用户令牌身份验证，则必须通过在应用注册详细信息页中的“清单”部分，将 `oauthEnableImplicitFlow` 设置为 true 进行启用。
    
       ![应用部件清单](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果应用程序使用服务器/应用程序身份验证，则转到应用注册中的“密钥”边栏选项卡，然后创建密码，或将公钥证书上传到应用注册。 如果创建了密码，保存后，复制密码供稍后使用，并安全存储它，因为将用它从 Azure AD 获取令牌。

       ![应用密钥](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>向 Azure Maps 授予 RBAC

将 Azure Maps 帐户与 Azure AD 租户关联后，可通过向变量 Azure Maps 访问控制角色分配用户或应用程序来授予访问控制权限。

1. 导航到“访问控制”选项，依次单击“角色分配”、“添加角色分配”。

    ![授予 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在“角色分配”弹出窗口中，选择“Azure Maps 日期读取器(预览版)”，然后依次单击“角色”、“将访问权限分配至”：Azure AD 用户、组或服务主体，从下拉列表中选择用户或应用程序，然后单击“保存”。

    ![添加角色分配](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>查看可用 Azure Maps RBAC 角色

若要查看可向其授予访问权限的 Azure Maps 基于角色的访问控制角色，请导航到“访问控制(IAM)”选项，单击“角色”，并搜索以 Azure Maps 开头的角色。

![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>查看 Azure Maps 基于角色的访问控制 (RBAC)

Azure AD 允许通过基于角色的访问控制 (RBAC) 的精细访问控制。 

若要查看已被授予用于 Azure Maps 的 RBAC 的用户或应用，请导航到“访问控制(IAM)”选项，选择“角色分配”，并按“Azure Maps”筛选。 下面将显示所有可用的角色。

![查看 RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

注册应用并与 Azure Maps 关联后，现在可请求访问令牌。

* 如果应用程序想要对 Azure Maps Web SDK (Web) 使用用户令牌身份验证，则需要使用 Azure Maps 客户端 ID 和 Azure AD 应用 ID 配置 html 页。

* 有关使用服务器/应用程序身份验证的应用程序，请从 Azure AD 登录终结点 `https://login.microsoftonline.com` 请求具有 Azure AD 资源 ID `https://atlas.microsoft.com/`、Azure Maps 客户端 ID、Azure AD 应用 ID 和 Azure AD 应用注册密码或证书的令牌。

有关为用户和服务主体从 Azure AD 请求访问令牌的详细信息，请参阅 [Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure AD 身份验证和 Azure Maps Web SDK 的详细信息，请参阅 [Azure AD 和 Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
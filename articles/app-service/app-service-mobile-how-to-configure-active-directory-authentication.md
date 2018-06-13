---
title: 为应用服务应用程序配置 Azure Active Directory 身份验证
description: 了解如何为应用服务应用程序配置 Azure Active Directory 身份验证。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 2530cb55cb054c02df5d55ccb86e959a061e2499
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155290"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>将应用服务应用配置为使用 Azure Active Directory 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文说明如何将 Azure 应用服务配置为使用 Azure Active Directory 作为身份验证提供程序。

## <a name="express"> </a>使用快速设置配置 Azure Active Directory
1. 在 [Azure 门户]中，导航到应用服务应用。 在左侧导航栏中，选择“身份验证/授权”。
2. 如果尚未启用“身份验证/授权”，请选择“启用”。
3. 选择“Azure Active Directory”，然后选择“管理模式”下的“快速”。
4. 选择“确定”，在 Azure Active Directory 中注册应用服务应用。 这会创建一个新的应用注册。 如果想要选择现有应用注册，请单击“选择现有应用”，并在租户中搜索以前创建的应用注册的名称。
   单击应用注册将其选中，然后单击“确定”。 然后在 Azure Active Directory 设置页上单击“确定”。
   默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
5. （可选）要限制只有通过 Azure Active Directory 身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Azure Active Directory 以进行身份验证。
6. 单击“ **保存**”。

现在，可以使用 Azure Active Directory 在应用服务应用中进行身份验证。

## <a name="advanced"> </a>（备用方法）使用高级设置手动配置 Azure Active Directory
也可以选择手动提供配置设置。 如果要使用的 AAD 租户不同于登录 Azure 所用的租户，这是较好的解决方案。 要完成配置，必须先在 Azure Active Directory 中创建注册，然后向应用服务提供一些注册详细信息。

### <a name="register"> </a>向 Azure Active Directory 注册应用服务应用
1. 登录 [Azure 门户]，并导航到应用服务应用。 复制应用 URL。 稍后要使用此信息配置 Azure Active Directory 应用注册。
2. 导航到“Active Directory”，选择“应用注册”，然后单击顶部的“新应用程序注册”开始新应用注册。 
3. 在“创建”页中，输入应用注册的“名称”，选择“Web 应用/API”类型，在“登录 URL”框中粘贴应用程序 URL（来自于步骤 1）。 然后单击“创建”。
4. 几秒钟后即可看到刚刚创建的新应用注册。
5. 添加应用注册后，单击应用注册名称，单击顶部的“设置”，然后单击“属性” 
6. 在“应用 ID URI”框中粘贴应用程序 URL（来自于步骤 1），并在“主页 URL”中粘贴应用程序 URL（来自于步骤 1），然后单击“保存”
7. 现在，单击“答复 URL”，编辑“答复 URL”，粘贴应用程序 URL（来自于步骤 1），修改协议，确保具有 https:// 协议（非 http://），然后在 URL 末尾追加 /.auth/login/aad/callback（例如 `https://contoso.azurewebsites.net/.auth/login/aad/callback`）。 单击“ **保存**”。   
8.  此时，复制应用的“应用程序 ID”。 保留此 ID 供将来使用。 需要使用它来配置应用服务应用。
9. 关闭“已注册应用”页。 在“应用注册”页中，单击顶部的“终结点”按钮，然后复制“联合元数据文档”URL。 
10. 通过粘贴并浏览到 XML 页，打开一个新的浏览器窗口并导航到 URL。 文档顶部是一个“EntityDescriptor”元素，应该有一个格式为 `https://sts.windows.net/` 的“entityID”属性，其后接特定于租户的 GUID（称为“租户 ID”）。 复制此值 - 它可作为证书颁发者 URL。 稍后配置应用程序时要用到此信息。

### <a name="secrets"> </a>将 Azure Active Directory 信息添加到应用服务应用
1. 返回 [Azure 门户]，导航到应用服务应用。 单击“身份验证/授权”。 如果“身份验证/授权”功能未启用，请切换为“打开”。 单击验证提供程序下的“Azure Active Directory”，配置应用。 （可选）默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。 将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 此选项会要求对所有请求进行身份验证，所有未经身份验证的请求都重定向到 Azure Active Directory 进行身份验证。
2. 在 Active Directory 身份验证配置中，单击“管理模式”下的“高级”。 将应用程序 ID（来自于步骤 8）粘贴到“客户端 ID”框中，并将 entityId（来自于步骤 10）粘贴到证书颁发者 URL 值。 然后单击“确定”。
3. 在 Active Directory 身份验证配置页上，单击“保存”。

现在，可以使用 Azure Active Directory 在应用服务应用中进行身份验证。

## <a name="optional-configure-a-native-client-application"></a>（可选）配置本机客户端应用程序
Azure Active Directory 还允许注册权限映射控制度更高的本机客户端。 如果想要使用 **Active Directory 身份验证库**等库进行登录，则需要这种注册。

1. 在 [Azure 门户]中，导航到“Active Directory”。
2. 在左侧导航栏中，选择“应用注册”。 在顶部单击“新建应用注册”。
4. 在“创建”页中，为应用注册输入“名称”。 在“应用程序类型”中选择“本机”。
5. 在“重定向 URI”框中，使用 HTTPS 方案输入站点的 */.auth/login/done* 终结点。 此值应类似于 *https://contoso.azurewebsites.net/.auth/login/done*。 如果要创建 Windows 应用程序，请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) 作为 URI。
5. 单击“创建”。
6. 在成功添加应用注册后，选择该应用注册将其打开。 找到应用程序 ID 并记下此值。
7. 单击“所有设置” > “所需的权限” > “添加” > “选择 API”。
8. 键入先前注册的应用服务应用的名称进行搜索，然后选中该应用并单击“选择”。 
9. 选择“访问 \<app_name>”。 然后单击“选择”。 然后单击“完成”。

现已配置可以访问应用服务应用的本机客户端应用程序。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
[alternative method]:#advanced

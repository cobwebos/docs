---
title: "如何为应用服务应用程序配置 Azure Active Directory 身份验证"
description: "了解如何为应用服务应用程序配置 Azure Active Directory 身份验证。"
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 25f0578a9e273c30ecc98af5b66c6dd43305aa03
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>如何将应用服务应用程序配置为使用 Azure Active Directory 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题说明如何将 Azure 应用服务配置为使用 Azure Active Directory 作为身份验证提供程序。

## <a name="express"> </a>使用快速设置配置 Azure Active Directory
1. 在 [Azure 门户]中，导航到应用程序。 依次单击“设置”和“身份验证/授权”。
2. 如果“身份验证/授权”功能未启用，请切换为“打开”。
3. 单击“Azure Active Directory”，并单击“管理模式”下面的“快速”。
4. 单击“确定”，在 Azure Active Directory 中注册应用程序。 随后会创建新注册。 如果想要选择现有注册，请单击“选择现有应用”，并在租户中搜索以前创建的注册的名称。
   单击注册以选中它，再单击“确定”。 然后在“Azure Active Directory 设置”边栏选项卡上单击“确定”。
   默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
5. （可选）要限制只有通过 Azure Active Directory 身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Azure Active Directory 以进行身份验证。
6. 单击“保存” 。

现在，可以使用 Azure Active Directory 在应用中进行身份验证。

## <a name="advanced"> </a>（备用方法）使用高级设置手动配置 Azure Active Directory
也可以选择手动提供配置设置。 如果要使用的 AAD 租户不同于登录 Azure 所用的租户，这是较好的解决方案。 要完成配置，必须先在 Azure Active Directory 中创建注册，然后向应用服务提供一些注册详细信息。

### <a name="register"> </a>将应用程序注册到 Azure Active Directory
1. 登录到 [Azure 门户]并导航到应用程序。 复制应用程序“URL”。 稍后要使用此信息配置 Azure Active Directory 应用。
2. 导航到“Active Directory”，选择“应用注册”，然后单击顶部的“新应用程序注册”开始新应用注册。 
3. 在“创建应用程序注册”对话框中，输入应用程序的“名称”，选择“Web 应用 API”类型，在“登录 URL”框中粘贴应用程序 URL（来自于步骤 1）。 然后单击“创建”。
4. 几秒钟后，将显示你刚刚创建的新应用程序注册。
5. 添加应用程序后，单击应用程序注册名称，单击顶部的“设置”，然后单击“属性” 
6. 在“应用 ID URI”框中粘贴应用程序 URL（来自于步骤 1），并在“主页 URL”中粘贴应用程序 URL（来自于步骤 1），然后单击“保存”
7. 现在，单击“答复 URL”，编辑“答复 URL”，粘贴应用程序 URL（来自于步骤 1），修改协议，确保具有 **https://** 协议 （非 http://） ，然后在 URL 末尾追加 /.auth/login/aad/callback。 （例如：`https://contoso.azurewebsites.net/.auth/login/aad/callback`。）单击“保存”。   
8.  此时，复制应用的“应用程序 ID”。 保留此 ID 供将来使用。 你将需要此 ID 来配置 Web 应用。
9. 关闭“应用程序注册详细信息”边栏选项卡。 返回到 Azure Active Directory 应用注册摘要，单击顶部的“终结点”按钮，然后复制“联合元数据文档”URL。 
10. 通过粘贴并浏览到 XML 页，打开一个新的浏览器窗口并导航到 URL。 文档顶部将是一个“EntityDescriptor”元素，应该有一个格式为 `https://sts.windows.net/` 的“entityID”属性，其后接特定于租户的 GUID（称为“租户 ID”）。 复制此值 - 它将作为**颁发者 URL**。 稍后配置应用程序时要用到此信息。

### <a name="secrets"></a>将 Azure Active Directory 信息添加到应用程序
1. 返回 [Azure 门户]，导航到应用程序。 单击“身份验证/授权”。 如果“身份验证/授权”功能未启用，请切换为“打开”。 单击验证提供程序下的“Azure Active Directory”，配置你的应用程序。 （可选）默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。 选择“请求未经身份验证时需执行的操作”，将其设置为“使用 Azure Active Directory 登录”。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Azure Active Directory 以进行身份验证。
2.在 Active Directory 身份验证配置中，单击“管理模式”下的“高级”。 将应用程序 ID（来自于步骤 8）粘贴到“客户端 ID”框中，并将 entityId（来自于步骤 10）粘贴到证书颁发者 URL 值。 然后单击“确定”。
3. 在 Active Directory 身份验证配置边栏选项卡上，单击“保存”。

现在，可以使用 Azure Active Directory 在应用中进行身份验证。

## <a name="optional-configure-a-native-client-application"></a>（可选）配置本机客户端应用程序
Azure Active Directory 还允许注册权限映射控制度更高的本机客户端。 如果想要使用 **Active Directory 身份验证库**等库进行登录，则需要这种注册。

1. 在 [Azure 经典门户]中导航到“Active Directory”。
2. 选择目录，并选择顶部的“应用程序”选项卡。 单击底部的“添加”，创建新的应用注册。
3. 单击“添加我的组织正在开发的应用程序”。
4. 在“添加应用程序”向导中，为应用程序输入“名称”，并单击“本机客户端应用程序”类型。 然后单击以继续。
5. 在“重定向 URI”框中，使用 HTTPS 方案输入站点的 */.auth/login/done* 终结点。 此值应类似于 *https://contoso.azurewebsites.net/.auth/login/done*。 如果要创建 Windows 应用程序，请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) 作为 URI。
6. 添加本机应用程序后，单击“配置”选项卡。找到**客户端 ID** 并记下此值。
7. 向下滚动到“针对其他应用程序的权限”部分，并单击“添加应用程序”。
8. 搜索以前注册的 Web 应用程序并单击加号图标。 然后单击复选标记以关闭对话框。 如果找不到 Web 应用程序，请导航到其注册并添加新的回复 URL（例如，当前 URL 的 HTTP 版本），单击“保存”，并重复这些步骤 - 应用程序应会显示在列表中。
9. 在刚刚添加的新项中，打开“委派的权限”下拉列表，并选择“访问(appName)”。 然后单击“保存”。

现已配置可以访问应用服务应用程序的本机客户端应用程序。

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
[Azure 经典门户]: https://manage.windowsazure.com/
[alternative method]:#advanced

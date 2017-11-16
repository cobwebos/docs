---
title: "使用 Azure AD 应用程序代理发布应用 | Microsoft Docs"
description: "在经典门户中使用 Azure AD 应用程序代理将本地应用程序发布到云。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 19f52181a2847ab52029adac4d58e402a76d5f30
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理发布应用程序

> [!div class="op_single_selector"]
> * [Azure 门户](application-proxy-publish-azure-portal.md)
> * [Azure 经典门户](active-directory-application-proxy-publish.md)

Azure AD 应用程序代理可发布要通过 Internet 访问的本地应用程序，从而帮助你为远程辅助角色提供支持。 至此，应该已 [在 Azure 经典门户中启用应用程序代理](active-directory-application-proxy-enable.md)。 本文指导完成发布要在本地网络上运行并提供从网络外部进行的安全远程访问的应用程序的步骤。 阅读完成这篇文章后，能够根据个性化信息或安全要求配置该应用程序。

> [!NOTE]
> 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。 若要使用应用程序代理，可以[在 Azure 门户中发布应用程序](application-proxy-publish-azure-portal.md)。

## <a name="publish-an-app-using-the-wizard"></a>使用向导发布应用
1. 在 [Azure 经典门户](https://manage.windowsazure.com/)中，以管理员身份进行登录。
2. 转到 Active Directory，并选择在其中启用了应用程序代理的目录。
   
    ![Active Directory - 图标](./media/active-directory-application-proxy-publish/ad_icon.png)
3. 单击“应用程序”选项卡，并单击屏幕底部的“添加”按钮
   
    ![添加应用程序](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
4. 选择“ **发布可从网络外部访问的应用程序**”。
   
    ![发布可从网络外部访问的应用程序](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)
5. 提供有关应用程序的以下信息：
   
   * **名称**：应用程序的用户友好名称。 它必须在目录中唯一。
   * **内部 URL**：这是应用程序代理连接器用于从专用网络内部访问应用程序的地址。 可以提供后端服务器上要发布的特定路径，而服务器的其余部分则不发布。 通过这种方式，可以在同一服务器上发布不同站点，并为每个站点提供其自己的名称和访问规则。
     
     > [!TIP]
     > 如果发布路径，请确保它包含应用程序的所有必要映像、脚本和样式表。 例如，如果应用位于 https://yourapp/app 并使用位于 https://yourapp/media 的映像，则应将 https://yourapp/ 发布为路径。
     > 
     > 
   * **预先身份验证方法**：应用程序代理如何在向用户提供对应用程序的访问权限之前验证用户。 从下拉菜单中选择一个选项。
     
     * Azure Active Directory：应用程序代理会重定向用户以便使用 Azure AD 登录，这会对他们针对目录和应用程序的权限进行身份验证。
     * Passthrough：用户不必进行身份验证即可访问应用程序。
     
     ![应用程序属性](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  
6. 若要完成向导，请单击屏幕底部的复选标记。 现已在 Azure AD 中定义应用程序。

## <a name="assign-users-and-groups-to-the-application"></a>将用户和组分配到应用程序
若要使用户可以访问发布的应用程序，需要单独或成组分配它们。 （请记住也为自己分配访问权限。）分配的每个用户都需要一个适用于 Azure Basic 或更高版本的许可证。 可以单独或成组分配许可证。 有关详细信息，请参阅[将用户分配到应用程序](active-directory-applications-guiding-developers-assigning-users.md)。 

对于需要预先身份验证的应用，分配用户时会授予使用应用程序的权限。 对于不需预先身份验证的应用来说，分配用户意味着用户可以通过访问面板访问该应用程序。

1. 完成“添加应用”向导之后，会看到适用于应用程序的快速启动页面。 若要管理有权访问应用的人员，请选择“ **用户和组**”。
   
    ![应用程序代理快速启动分配用户 — 屏幕截图](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)
2. 在目录中搜索特定组，或显示所有用户。 若要显示搜索结果，请单击复选标记。
   
      ![搜索组或用户 — 屏幕截图](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)
3. 选择要分配到此应用的每个用户或组，并单击“ **分配**”。 系统会要求确认此操作。

> [!NOTE]
> 对于集成 Windows 身份验证应用，只能分配已从本地 Active Directory 同步的用户和组。 对于使用 Azure Active Directory 应用程序代理发布的应用，不能分配使用 Microsoft 帐户登录的用户以及来宾。 确保用户使用属于要发布的应用所在域的一部分的凭据登录。
> 
> 

## <a name="test-your-published-application"></a>测试已发布的应用程序
一旦发布应用程序，即可通过导航到已发布的 URL 对其进行测试。 请确保可以访问它，能够正确呈现且一切按预期工作。 如果遇到问题或收到一条错误消息，请尝试查看 [故障排除指南](active-directory-application-proxy-troubleshoot.md)。

## <a name="configure-your-application"></a>配置应用程序
可以在“配置”页面上修改发布的应用或设置高级选项。 在此页面上，可以通过更改名称或上传徽标来自定义应用。 还可以管理访问规则，如预先身份验证方法或 Multi-Factor Authentication。

![高级配置](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)

使用 Azure Active Directory 应用程序代理发布应用程序后，这些应用程序将出现在 Azure AD 的“应用程序”列表中，可以在该处管理这些应用程序。

如果在发布应用程序后禁用应用程序代理服务，则不再可以从专用网络外部访问应用程序。 用户仍可在本地正常访问应用程序。

若要查看应用程序并确保它可供访问，请双击应用程序的名称。 如果“应用程序代理”服务已禁用并且该应用程序不可用，则会在屏幕顶部显示一条警告消息。

要删除某个应用程序，请在列表中选择该应用程序，并单击“ **删除**”。

## <a name="next-steps"></a>后续步骤
* [使用自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [启用条件性访问](application-proxy-enable-remote-access-sharepoint.md)
* [使用声明感知应用程序](active-directory-application-proxy-claims-aware-apps.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)


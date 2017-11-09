---
title: "Azure Active Directory Identity Protection 和 Microsoft Graph 入门 | Microsoft 文档"
description: "介绍如何在 Microsoft Graph 中查询 Azure Active Directory 提供的风险事件列表和相关信息。"
services: active-directory
keywords: "azure active directory identity protection, 风险事件, 漏洞, 安全策略, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 568cad4e394dfedddce1bfce66ddf627947d7568
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory Identity Protection 和 Microsoft Graph 入门
Microsoft Graph 是 Microsoft 的统一 API 终结点，并且是 [Azure Active Directory Identity Protection](active-directory-identityprotection.md) API 的主页。 使用第一个 API **identityRiskEvents** 可以在 Microsoft Graph 中查询[风险事件](active-directory-identityprotection-risk-events-types.md)列表和相关信息。 可以参考本文开始查询此 API。 如需深入介绍、完整文档和访问 Graph 浏览器，请参阅 [Microsoft Graph 站点](https://graph.microsoft.io/)。

> [!IMPORTANT]
> Microsoft 建议使用 Azure 门户而不是本文中引用的 Azure 经典门户通过 [Azure AD 管理中心](https://aad.portal.azure.com)管理 Azure AD。

通过 Microsoft Graph 访问 Identity Protection 数据需要执行三个步骤：

1. 使用客户端机密添加应用程序。 
2. 使用此密钥和其他一些信息片段在 Microsoft Graph 中进行身份验证，并在其中接收身份验证令牌。 
3. 使用此令牌可对 API 终结点发出请求，并取回 Identity Protection 数据。

准备工作：

* 需有管理员权限才能在 Azure AD 中创建应用程序
* 租户域的名称（例如 contoso.onmicrosoft.com）。

## <a name="add-an-application-with-a-client-secret"></a>使用客户端机密添加应用程序
1. 以管理员身份[登录](https://manage.windowsazure.com)到 Azure 经典门户。 
2. 在左侧导航窗格中，单击“Active Directory”。 
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. 从“目录”列表中，选择要为其启用目录集成的目录。
4. 在顶部菜单中，单击“应用程序”。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. 在页面底部单击“添加”。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. 在“要执行什么操作?”对话框中，单击“添加我的组织正在开发的应用程序”。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. 在“向我们说明应用程序”对话框中，执行以下步骤：
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. 在“名称”文本框中，键入应用程序的名称（例如“AADIP 风险事件 API 应用程序”）。
   
    b. 在“类型”中，选择“Web 应用程序和/或 Web API”。
   
    c. 单击“下一步”。
8. 在“应用属性”对话框中，执行以下步骤：
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. 在“登录 URL”文本框中，键入 `http://localhost`。
   
    b. 在“应用 ID URI”文本框中，键入 `http://localhost`。
   
    c. 单击“完成”。

现在可以配置应用程序。

![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>授予应用程序使用 API 的权限
1. 在应用程序页面上的顶部菜单中，单击“配置”。 
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. 在“针对其他应用程序的权限”部分中，单击“添加应用程序”。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. 上“针对其他应用程序的权限”对话框中执行以下步骤：
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. 选择“Microsoft Graph”。
   
    b. 单击“完成”。
4. 单击“应用程序权限: 0”，并选择“读取所有标识风险事件信息”。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. 单击页面底部的“保存”  。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>获取访问密钥
1. 在应用程序页面上的“密钥”部分中，选择“1 年”作为持续时间。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. 单击页面底部的“保存”  。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. 在“密钥”部分中，复制新建密钥的值，然后将其粘贴到一个安全的位置。
   
    ![创建应用程序](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > 如果丢失此密钥，必须返回到此部分并创建新密钥。 将此密钥保密：持有该密钥的任何人都可以访问数据。
   > 
   > 
4. 在“属性”部分中复制“客户端 ID”，然后将其粘贴到一个安全的位置。 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>在 Microsoft Graph 中进行身份验证并查询标识风险事件 API
现在应已获得：

* 前面复制的客户端 ID
* 前面复制的密钥
* 租户域的名称

若要进行身份验证，请向 `https://login.microsoft.com` 发送一个 post 请求，并在请求正文中包含以下参数：

* grant_type：“**client_credentials**”
* resource：“**https://graph.microsoft.com**”
* client_id：<your client ID>
* client_secret：<your key>

> [!NOTE]
> 需要提供 **client_id** 和 **client_secret** 参数的值。
> 
> 

如果成功，此命令将返回身份验证令牌。  
若要调用 API，请创建包含以下参数的标头：

    `Authorization`=”<token_type> <access_token>"


身份验证时，可以在返回的令牌中找到令牌类型和访问令牌。

将此标头作为请求发送到以下 API URL：`https://graph.microsoft.com/beta/identityRiskEvents`

如果成功，响应中会包含标识风险事件的集合，以及采用 OData JSON 格式的相关数据，在适当的情况下可以分析和处理这些数据。

下面是使用 Powershell 进行身份验证和调用API 的示例代码。  
只需添加客户端 ID、密钥和租户域即可。

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>后续步骤
恭喜，已向 Microsoft Graph 发出了第一个调用！  
现在，可以在适当的情况下查询标识风险事件和使用数据。

若要详细 Microsoft Graph 以及如何使用图形 API 构建应用程序，请查看[文档](https://graph.microsoft.io/docs)。同时，[Microsoft Graph 站点](https://graph.microsoft.io/)上提供了更丰富的信息。 另外，强烈建议将 [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) 网页加入书签，因为其中列出了可在 Graph 中使用的所有 Identity Protection API。 在我们添加了通过 API 使用 Identity Protection 的新方法后，该网页上也会介绍这些方法。

## <a name="additional-resources"></a>其他资源
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Azure Active Directory Identity Protection 检测到的风险事件类型](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Microsoft Graph 概述](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection 服务根](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)


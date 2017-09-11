---
title: "使用 Azure AD 应用程序代理为发布的应用设置自定义主页 | Microsoft 文档"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 9069166259265f5d2b43043b75039e239f397f6c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理为发布的应用设置自定义主页

本文讨论如何配置应用以将用户定向到自定义主页。 当你使用应用程序代理发布应用程序时，你需要设置内部 URL，但有时不是你的用户首先看到的页面。 设置自定义主页，以便你的用户通过 Azure Active Directory 访问面板或 Office 365 应用启动程序访问应用时转到相应页面。

当用户启动应用时，他们默认会定向到已发布应用的根域 URL。 登录页通常设置为主页 URL。 如果希望应用用户登录到应用中的特定页面，请使用 Azure AD PowerShell 模块来定义自定义主页 URL。 

例如：
- 在公司网络内，用户转到 *https://ExpenseApp/login/login.aspx* 来登录并访问你的应用。
- 因为你有其他资产（如应用程序代理需要在文件夹结构顶层访问的映像），因此，你需要使用 https://ExpenseApp 作为内部 URL 发布应用。
- 默认外部 URL 是 https://ExpenseApp-contoso.msappproxy.net，不会将你的用户定向至登录页面。  
- 将 https://ExpenseApp-contoso.msappproxy.net/login/login.aspx 设置为主页 URL，以为你的用户提供无缝体验。 

>[!NOTE]
>向用户提供对已发布应用的访问权限时，会在 [Azure AD 访问面板](active-directory-saas-access-panel-introduction.md)和 [Office 365 应用启动器](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)中显示这些应用。

## <a name="before-you-start"></a>开始之前

在设置主页 URL 之前，请注意以下要求：

* 确保指定的路径是根域 URL 的子域路径。

  例如，如果根域 URL 为 https://apps.contoso.com/app1/，则配置的主页 URL 必须以 https://apps.contoso.com/app1/ 开头。

* 如果对发布的应用做了更改，这种更改可能会重置主页 URL 的值。 将来更新应用时，应该重新检查并根据需要更新主页 URL。

## <a name="change-the-home-page-in-the-azure-portal"></a>更改 Azure 门户的主页

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “应用注册”，然后从列表中选择应用程序。 
3. 从设置中选择“属性”。
4. 使用新路径更新“主页 URL”字段。 

   ![提供新主页 URL](./media/application-proxy-office365-app-launcher/homepage.png)

5. 选择“保存”

## <a name="change-the-home-page-with-powershell"></a>使用 PowerShell 更改主页

### <a name="install-the-azure-ad-powershell-module"></a>安装 Azure AD PowerShell 模块

在使用 PowerShell 定义自定义主页 URL 之前，请安装 Azure AD PowerShell 模块。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131)下载使用图形 API 终结点的包。 

若要安装包，请执行以下步骤：

1. 打开标准 PowerShell 窗口，并运行以下命令：

    ```
     Install-Module -Name AzureAD
    ```
    如果以非管理员身份运行该命令，请使用 `-scope currentuser` 选项。
2. 在安装期间，请选择“Y”安装来自 Nuget.org 的两个包。这两个包是必需的。 

### <a name="find-the-objectid-of-the-app"></a>查找应用的 ObjectID

获取应用的 ObjectID，并按主页搜索该应用。

1. 打开 PowerShell 并导入 Azure AD 模块。

    ```
    Import-Module AzureAD
    ```

2. 以租户管理员身份登录到 Azure AD 模块。

    ```
    Connect-AzureAD
    ```
3. 基于其主页 URL 查找应用。 可以通过在门户中转到“Azure Active Directory” > “企业应用程序” > “所有应用程序”找到 URL。 此示例使用 *sharepoint-iddemo*。

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. 随后应会返回如下所示的结果。 复制 ObjectID GUID，以便在下一部分中使用。

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>更新主页 URL

在步骤 1 所使用的同一 PowerShell 模块中，执行以下步骤：

1. 确认应用正确，将 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 替换为在前一步骤中复制的 ObjectID。

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 确认应用后，便可以按如下所述更新主页。

2. 创建一个空白应用程序对象用于保存想要做出的更改。 此变量包含你想要更新的值。 在此步骤中没有创建任何内容。

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. 将主页 URL 设置为所需的值。 该值必须是已发布应用的子域路径。 例如，如果将主页 URL 从 https://sharepoint-iddemo.msappproxy.net/ 更改为 https://sharepoint-iddemo.msappproxy.net/hybrid/，应用用户将直接转到自定义主页。

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. 使用在“步骤 1：查找应用的 ObjectID”中复制的 GUID (ObjectID) 进行更新。

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. 若要确认更改是否成功，请重新启动应用。

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>对应用进行任何更改都可能会重置主页 URL。 如果你主页的 URL 重置，请重复步骤 2。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-enable-remote-access-sharepoint.md)
- [在 Azure 门户中启用应用程序代理](active-directory-application-proxy-enable.md)


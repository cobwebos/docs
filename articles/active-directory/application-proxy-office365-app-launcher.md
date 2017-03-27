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
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5818efb315a0452beea03cde1adc657a9520dcac
ms.openlocfilehash: 1fe3f3a697618bec5d314c6ebf161da37efc1346
ms.lasthandoff: 03/01/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理为发布的应用设置自定义主页

本文介绍如何将应用配置为在用户从 Azure Active Directory (Azure AD) 访问面板和 Office 365 应用启动器访问应用时，将用户定向到自定义主页。

>[!NOTE]
>应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
>

如果希望用户登陆到应用中的特定页面（例如 *https://expenseApp-contoso.msappproxy.net/login/login.aspx*），可以使用 Azure AD PowerShell 模块定义实例的自定义主页 URL。

>[!NOTE]
>向用户提供对已发布应用的访问权限时，将在 [Azure AD 访问面板](active-directory-saas-access-panel-introduction.md)和 [Office 365 应用启动器](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)中显示这些应用。
>

当用户启动应用时，他们默认会定向到已发布应用的根域 URL。 登陆页通常设置为主页 URL。 例如，对于后端应用 http://ExpenseApp，URL 将发布为 *https://expenseApp-contoso.msappproxy.net*。 默认情况下，主页 URL 设置为 *https://expenseApp-contoso.msappproxy.net*。

## <a name="determine-the-home-page-url"></a>确定主页 URL

在设置主页 URL 之前，请注意以下事项：

* 确保指定的路径是根域 URL 的子域路径。

 例如，如果可从根域 URL（例如 https://intranet-contoso.msappproxy.net/）访问发布的应用，则配置的主页 URL 必须以 https://intranet-contoso.msappproxy.net/ 开头。

* 例如，如果根域 URL 为 https://apps.contoso.com/app1/，则配置的主页 URL 必须以 https://apps.contoso.com/app1/ 开头。

* 如果对发布的应用做了更改，这种更改可能会重置主页 URL 的值。 因此，如果确定要更新应用，应该反复检查并根据需要更新主页 URL。

下一部分演练如何为发布的应用设置自定义主页 URL。 

## <a name="install-the-azure-ad-powershell-module"></a>安装 Azure AD PowerShell 模块

在使用 PowerShell 定义自定义主页 URL 之前，请安装 Azure AD PowerShell 模块的非标准包。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)下载使用图形 API 终结点的包。 

若要使用 PowerShell 安装该包，请执行以下操作：

1. 打开标准 PowerShell 窗口，然后运行以下命令：

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    如果以非管理员身份运行该命令，请使用 **-scope currentuser** 选项。
2. 在安装期间，请选择“Y”安装来自 Nuget.org 的两个包。 这两个包是必需的。 

## <a name="set-a-custom-home-page-url-by-using-the-azure-ad-powershell-module"></a>使用 Azure AD PowerShell 模块设置自定义主页 URL

安装 Azure AD PowerShell 模块后，便可以设置主页 URL。 为此，请遵循后面两个部分中的说明。

### <a name="step-1-find-the-objectid-of-the-app"></a>步骤 1：查找应用的 ObjectID

获取应用的 ObjectID，然后按主页搜索该应用。

1. 打开 PowerShell，然后使用以下命令导入 Azure AD 模块：

    ```
    Import-Module AzureAD
    ```

2. 使用以下 cmdlet 登录到 Azure AD 模块，然后遵照屏幕上的说明操作。 请务必以租户管理员的身份登录。

    ```
    Connect-AzureAD
    ```
3. 使用此 cmdlet 可以根据包含 *sharepoint-iddemo* 的主页查找应用。 若要编辑应用，请将以下值替换为适用于该应用的值。

    ```
    Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
    ```
4. 随后应会返回如下所示的结果。 复制 GUID (ObjectID)，以便在“步骤 2：更新主页 URL”中使用。

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="step-2-update-the-home-page-url"></a>步骤 2：更新主页 URL

在“步骤 1：查找应用的 ObjectID”中使用的同一个 PowerShell 模块中执行以下操作：

1. 确认应用正确，将 *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* 替换为在前一步骤中复制的 GUID (ObjectID)。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 确认应用后，便可以按如下所述更新主页。

2. 创建一个空白应用程序对象用于保存想要做出的更改。  

 >[!NOTE]
 >这只是一个用于保存想要更新的值的变量，因此，实际上并未创建任何对象。

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. 将主页 URL 设置为所需的值。 该值必须是已发布应用的子域路径。 例如，如果将主页 URL 从 *https://sharepoint-iddemo.msappproxy.net/* 更改为 *https://sharepoint-iddemo.msappproxy.net/hybrid/*，应用用户将直接转到自定义主页。

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. 使用在“步骤 1：查找应用的 ObjectID”中复制的 GUID (ObjectID) 进行更新。

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. 若要确认更改是否成功，请重新启动应用。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>对应用进行任何更改可能会重置主页 URL。 如果发生这种情况，请重复“步骤 2：更新主页 URL”中的过程。

## <a name="next-steps"></a>后续步骤

[使用 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-enable-remote-access-sharepoint.md)<br>
[在 Azure 门户中启用应用程序代理](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)


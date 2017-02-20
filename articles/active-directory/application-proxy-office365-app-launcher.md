---
title: "使用 Azure AD 应用代理为发布的应用程序设置自定义主页 | Microsoft 文档"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识。"
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
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>使用 Azure AD 应用代理为发布的应用程序设置自定义主页

本文介绍如何将应用程序配置为在用户从 Azure AD 访问面板和 Office 365 应用启动器访问你的应用程序时，将用户定向到自定义主页。

>[!NOTE]
>应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
> 
 
如果希望用户登陆到应用程序中的特定页面（例如 https://expenseApp-contoso.msappproxy.net/login/login.aspx），可以使用 Azure AD Powershell 模块定义这些实例的自定义主页 URL。

>[!NOTE]
>向用户提供已发布应用程序的访问权限时，将在 [Azure AD 访问面板](active-directory-saas-access-panel-introduction.md)和 [Office 365 应用启动器](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)中显示你的应用。 
>

当用户启动该应用时，他们默认会定向到已发布应用的根域 URL。 登陆页通常设置为主页 URL。 例如，后端应用 http://ExpenseApp 将发布为 https://expenseApp-contoso.msappproxy.net。 默认情况下，主页 URL 设置为 https://expenseApp-contoso.msappproxy.net。

## <a name="determine-the-home-page-url"></a>确定主页 URL

设置主页 URL 之前需要注意几个事项：

* 必须确保指定的路径是根域 URL 的子域路径。

 例如，如果可从主页 URL https://intranet-contoso.msappproxy.net/ 访问发布的应用，则配置的主页 URL 必须以 https://intranet-contoso.msappproxy.net/ 开头。 
 
* 如果主页 URL 为 https://apps.contoso.com/app1/，则主页 URL 必须以 https://apps.contoso.com/app1/ 开头。

* 如果对发布的应用程序做了更改，可能会重置主页 URL 的值。 因此，如果确定要更新应用程序，应该反复检查并根据需要更新主页 URL。


下一部分演练如何为发布的应用程序设置自定义主页 URL。 

## <a name="install-the-azure-ad-powershell-module"></a>安装 Azure AD PowerShell 模块

在使用 Powershell 定义自定义主页 URL 之前，首先需要安装 Azure AD PowerShell 模块的非标准包。  可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)下载这个使用图形 API 终结点的包。 

**使用 Powershell 安装该包：**

1. 打开标准 PowerShell。
2. 运行以下命令：

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 如果以非管理员身份运行此命令，需要使用 _-scope currentuser_ 选项。
3. 在安装期间，请选择“Y”安装来自 Nuget.org 的两个包。  若要使用该包，需要执行上述两项操作。 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>使用 Azure AD Powershell 模块设置自定义主页 URL

安装 Azure AD Powershell 模块后，可以使用两个简单的步骤来设置主页 URL。

1. 查找想要更新的应用程序。
2. 更新应用程序的主页 URL。

###<a name="step-1--find-the-objectid-of-the-application"></a>步骤 1 – 查找应用程序的 ObjectID

首先必须获取应用程序的 ObjectID，然后按主页搜索该应用程序。

1. 打开 PowerShell。
2. 导入 Azure AD 模块。
  
 ```
 Import-Module AzureAD
 ```
3. 登录到 Azure AD 模块。  使用以下 cmdlet，然后遵照屏幕上的说明操作。 请确保以租户管理员的身份登录。
 
 ```
 Connect-AzureAD
 ```
4. 以下 cmdlet 根据包含 _sharepoint-iddemo_ 的主页查找应用程序。 这就是你想要编辑的应用。 需要将此值替换为适用于该应用程序的值。
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. 应会看到类似于以下响应的结果。 GUID（下面的 ObjectID 值）是需要复制的项。
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. 复制 GUID (ObjectID) 值。 下一步骤需要用到此值。


###<a name="step-2--update-the-homepage-url"></a>步骤 2 – 更新主页 URL

像查找应用程序 ID 时一样，使用相同的 PowerShell 模块来更新主页 URL。 登录到 PowerShell 后，请执行以下步骤：

1. 确认应用程序正确，将 _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ 替换为在前面的步骤 1 中从应用程序复制的 ObjectID 值。 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 确认应用程序后，便可以按如下所述更新主页。
 
2. 创建一个空白应用程序对象用于保存想要做出的更改。 这只是一个用于保存想要更新的值的变量，因此，实际上并未创建任何对象。
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. 将主页设置为所需的值。 请记住，它必须是发布的应用程序的子域路径。 例如，如果将主页从 _https://sharepoint-iddemo.msappproxy.net/_ 更改为 _https://sharepoint-iddemo.msappproxy.net/hybrid/_，用户将直接转到自定义主页。
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. 需要执行的最后一项操作就是做出更新。 请记得使用前面步骤 1 中复制的 GUID。
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. 现在，需要通过再次启动应用程序来确认自定义主页，验证更改是否成功。
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>请记住，对应用程序进行任何更改都可能会重置主页 URL。 在这种情况下，需要重复此过程。

##<a name="next-steps"></a>后续步骤

[使用 Azure AD 应用代理启用对 SharePoint 的远程访问](application-proxy-enable-remote-access-sharepoint.md)<br>
[在 Azure 门户中启用应用程序代理](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->



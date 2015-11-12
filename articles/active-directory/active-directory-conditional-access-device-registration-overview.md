<properties
	pageTitle="Azure Active Directory 设备注册概述 | Microsoft Azure"
	description="是基于设备的条件性访问方案的基础。在注册设备时，Azure Active Directory 设备注册会为设备设置一个标识，用以在用户登录时对设备进行身份验证。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="08/02/2015"

# Azure Active Directory 设备注册概述

Azure Active Directory 设备注册是基于设备的条件性访问方案的基础。在注册设备时，Azure Active Directory 设备注册会为设备设置一个标识，用以在用户登录时对设备进行身份验证。然后，可以使用已经过身份验证的设备和设备的属性，对云中和本地托管的应用程序实施条件性访问策略。当与 Intune 之类的移动设备管理解决方案结合使用时，Azure Active Directory 中的设备属性将使用关于设备的更多信息进行更新。这将允许你创建条件性访问规则，用于从设备强制实施将满足你的安全和合规性标准的访问。Azure Active Directory 设备注册在 Azure Active Directory 中可用。该服务支持 iOS、Android 和 Windows 设备。利用 Azure Active Directory 设备注册的各个方案可能有更具体的要求和平台支持。请继续阅读以了解有关目前可用的具体方案的更多信息。

## Azure Active Directory 设备注册支持的方案

可以将 Azure AD 设备注册视为各种方案的基础。通常，该服务支持 iOS、Android 和 Windows 设备。利用 Azure AD 设备注册的各个方案可能有更具体的要求和平台支持。这些方案如下所述：对在本地托管的应用程序的条件性访问：你可以将已注册的设备与配置为将 AD FS 与 Windows Server 2012 R2 一起使用的应用程序的访问策略一起使用。有关为本地部署设置条件性访问的更多详细信息，请参阅“使用 Azure Active Directory 设备注册设置条件性访问”。

使用 Microsoft Intune 的 Office 365 应用程序的条件性访问：IT 管理员可以设置条件性访问设备策略来保护公司资源，同时允许信息工作者在符合条件的设备上访问服务。有关详细信息，请参阅“Office 365 服务的条件性访问设备策略”

## 设置 Azure Active Directory 设备注册

以下设置可用于 Azure Active Directory 设备注册服务：
在 Azure 门户中启用 Azure AD 设备注册。

Windows 设备通过查找已知的 DNS 记录来发现该服务。你必须对你的公司的 DNS 进行配置，以便 Windows 7 和 Windows 8.1 设备可以发现和使用该服务。

你可以使用 Azure Active Directory 中的管理员门户查看和启用/禁用已注册的设备。

## 启用 Azure Active Directory 设备注册
以下部分介绍了如何为你的目录启用 Azure Active Directory 设备注册服务。
启用 Azure Active Directory 设备注册服务
-------------------------------------------------------------
1. 以管理员身份登录到 Azure 门户。
1. 在左窗格中选择“Active Directory”。
1. 在“目录”选项卡中选择你的目录。
1. 选择“配置”选项卡。
1. 滚动到名为“设备”的部分。
1. 为“用户可以加入工作区的设备”选择“全部”。
1. 选择你想要按每个用户授权的设备的最大数目。

>[AZURE.NOTE]
>注册 Microsoft Intune 或适用于 Office 365 的移动设备管理需要加入工作区。如果你已配置其中的任一服务，则会选中“全部”并禁用“无”按钮。


默认情况下，不会为该服务启用双重身份验证。但是，在注册设备时，建议启用双重身份验证。

* 在为该服务请求双重身份验证之前，你必须在 Azure Active Directory 中配置一个双重身份验证提供程序并为你的用户帐户配置 Multi-Factor Authentication，请参阅“将多重身份验证添加到 Azure Active Directory”

* 如果你将 AD FS 与 Windows Server 2012 R2 一起使用，则必须在 AD FS 中配置一个双重身份验证模块，请参阅“将 Multi-Factor Authentication 与 Active Directory 联合身份验证服务配合使用”

## 配置 Azure Active Directory 设备注册发现
Windows 7 和 Windows 8.1 设备将通过组合使用用户帐户名称和已知的设备注册服务器名称来发现设备注册服务器。必须创建一条 DNS CNAME 记录，该记录指向与你的 Azure Active Directory 设备注册服务关联的 A 记录。该 CNAME 记录必须使用已知的前缀 enterpriseregistration，后接组织中的用户帐户使用的 UPN 后缀。如果你的组织使用了多个 UPN 后缀，则必须在 DNS 中创建多条 CNAME 记录。

例如，如果你在组织中使用名为 @contoso.com 和 @region.contoso.com 的两个 UPN 后缀，则需创建以下 DNS 记录。
 
| 条目 | 类型 | 地址 |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## 查看和管理 Azure Active Directory 中的设备对象
1. 在 Azure 管理员门户中，你可以查看、阻止和解除阻止设备。被阻止的设备不再能够访问配置为只允许已注册设备访问的应用程序。
1. 以管理员身份登录到 Microsoft Azure 门户。
1. 在左窗格中选择“Active Directory”。
1. 选择你的目录。
1. 选择“用户”选项卡。然后，选择要查看其设备的用户。
1. 选择“设备”选项卡。
1. 从下拉菜单中选择“注册的设备”。
1. 在这里你可以查看、阻止或解除阻止用户的已注册设备。 

## 其他主题

你可以向 Azure AD 设备注册注册你的已加入 Windows 7 和 Windows 8.1 域的设备。以下主题详细说明了在 Windows 7 和 Windows 8.1 设备上配置设备注册所要满足的先决条件和需要执行的步骤。将已加入 Windows 域的设备自动注册到 Azure Active Directory

<!---HONumber=79-->
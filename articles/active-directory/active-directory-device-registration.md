---
title: "将已加入 Windows 域的设备自动注册到 Azure Active Directory | Microsoft Docs"
description: "IT 管理员可以选择以自动静默方式向 Azure Active Directory (Azure AD) 注册已加入域的 Windows 设备。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 89f2498419e611011dc7817f4ecdea6f4a84c9bc
ms.lasthandoff: 03/10/2017


---
# <a name="automatic-device-registration-with-azure-active-directory-for-windows-domain-joined-devices"></a>将已加入域的 Windows 设备自动注册到 Azure Active Directory
作为 IT 管理员，你可以选择以自动静默方式向 Azure Active Directory (Azure AD) 注册已加入 Windows 域的设备。 如果你已针对 Office365 应用程序或者由 AD FS 在本地管理的应用程序配置了基于设备的条件性访问策略，则这种方式非常有效。 可以阅读 [Azure Active Directory 设备注册概述](active-directory-device-registration-get-started.md)来详细了解设备注册方案。

> [!NOTE]
>  有关如何设置自动设备注册的最新说明，请参阅[如何使用 Azure Active Directory 设置已加入 Windows 域的设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)。
> 
> 

向 Azure Active Directory 自动注册设备适用于已加入 Active Directory 域的 Windows 7 和 Windows 8.1 计算机。 这通常是提供给信息工作者使用的企业自有计算机。

若要开始向 Azure AD 注册已加入域的 Windows 设备，需确保满足以下先决条件。 满足先决条件后，请为加入域的 Windows 设备配置自动设备注册。

## <a name="deploy-ad-fs-and-connect-to-azure-active-directory-using-azure-active-directory-connect"></a>部署 AD FS 并使用 Azure Active Directory Connect 连接到 Azure Active Directory
1. 使用 Azure Active Directory Connect 将 Active Directory 联合身份验证服务 (AD FS) 部署到 Windows Server 2012 R2，并设置与 Azure Active Directory 的联合关系。
2. 配置其他 Azure Active Directory 信赖方信任声明规则。
3. 打开 AD FS 管理控制台，并导航到“AD FS”>“信任关系”>“信赖方信任”。 右键单击“Microsoft Office 365 标识平台”信赖方信任对象，并选择“编辑声明规则...”
4. 在“颁发转换规则”选项卡中，选择“添加规则”。
5. 从“声明规则模板”下拉框中选择“使用自定义规则发送声明”。 选择“下一步”。
6. 在“声明规则名称:”文本框中键入“身份验证方法声明规则”。
7. 在“声明规则:”文本框中键入以下声明规则：
   
        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
8. 单击“确定”两次以完成对话框中的操作。

## <a name="configure-an-additional-azure-active-directory-relying-party-trust-authentication-class-reference"></a>配置其他 Azure Active Directory 信赖方信任身份验证类引用
在联合服务器上，打开 Windows PowerShell 命令窗口并键入：

  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

其中，<RPObjectName> 是 Azure Active Directory 信赖方信任对象的信赖方对象名称。 此对象通常命名为 Microsoft Office 365 标识平台。

## <a name="ad-fs-global-authentication-policy"></a>AD FS 全局身份验证策略
配置 AD FS 全局主要身份验证策略以允许 Intranet 使用 Windows 集成身份验证（这是默认设置）。

## <a name="internet-explorer-configuration"></a>Internet Explorer 配置
在 Windows 设备的 Internet Explorer 中，为“本地 Intranet”安全区域配置以下设置：

* 只存在一个证书时不提示进行客户端证书选择：**已启用**
* 允许脚本：**已启用**
* 只在 Intranet 区域自动登录：**已选中**

这些是 Internet Explorer“本地 Intranet”安全区域的默认设置。 你可以通过导航到“Internet 选项” > “安全”>“本地 Intranet”>“自定义级别”来查看或管理这些设置。 你也可以使用 Active Directory 组策略配置这些设置。

## <a name="network-connectivity"></a>网络连接
加入域的 Windows 设备必须已连接到 AD FS 和 Active Directory 域控制器，才能自动向 Azure AD 注册。 这通常意味着计算机必须已连接到企业网络。 连接方式包括有线连接、Wi-Fi 连接、DirectAccess 或 VPN。

## <a name="configure-azure-active-directory-device-registration-discovery"></a>配置 Azure Active Directory 设备注册发现
Windows 7 和 Windows 8.1 设备将通过组合使用用户帐户名称和已知的设备注册服务器名称来发现设备注册服务器。 必须创建一条 DNS CNAME 记录，该记录指向与你的 Azure Active Directory 设备注册服务关联的 A 记录。 该 CNAME 记录必须使用已知前缀 **enterpriseregistration**，后接组织中用户帐户使用的 UPN 后缀。 如果你的组织使用了多个 UPN 后缀，则必须在 DNS 中创建多条 CNAME 记录。

例如，如果在组织中使用名为 @contoso.com 和 @region.contoso.com 的两个 UPN 后缀，则需创建以下 DNS 记录。

| 条目 | 类型 | 地址 |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="configure-automatic-device-registration-for-windows-7-and-windows-81-domain-joined-devices"></a>为已加入 Windows 7 和 Windows 8.1 域的设备配置自动设备注册
使用以下链接为加入 Windows 7 和 Windows 8.1 域的设备配置自动设备注册。 继续下一步之前，请确保满足上述先决条件。

* [如何使用 Azure Active Directory 配置已加入 Windows 域的设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="additional-notes"></a>附加说明
向 Azure AD 注册设备可让设备全面发挥功能。 使用 Azure AD 设备注册可以注册个人 (BYOD) 移动设备和公司所拥有的已加入域的设备。 设备可以配合托管服务（例如 Office365 和由 AD FS 在本地管理的服务）一起使用。

如果公司使用移动设备和传统设备，或使用 Office365、Azure AD 或其他 Microsoft 服务，则应该使用 Azure AD 设备注册服务在 Azure AD 中注册设备。如果公司不使用移动设备，也不使用任何 Microsoft 服务（例如 Office365、Azure AD 或 Microsoft Intune），而只是托管本地应用程序，则可以选择使用 AD FS 在 Active Directory 中注册设备。

可以在[此处](https://technet.microsoft.com/library/dn486831.aspx)详细了解如何使用 AD FS 部署设备注册。

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 设备注册概述](active-directory-device-registration-get-started.md)

* [如何使用 Azure Active Directory 配置已加入 Windows 域的设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)



---
title: "如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册 | Microsoft Docs"
description: "以自动静默方式向 Azure Active Directory 注册已加入域的 Windows 设备。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.lasthandoff: 03/10/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Azure Active Directory 设备注册入门

Azure Active Directory 设备注册是基于设备的条件访问方案的基础。 在注册设备时，Azure Active Directory 设备注册会为设备提供一个标识，用于在用户登录时对设备进行身份验证。 然后，可以使用已经过身份验证的设备和设备的属性，对云中和本地托管的应用程序实施条件性访问策略。

当与 Microsoft Intune 之类的移动设备管理 (MDM) 解决方案结合使用时，Azure Active Directory 中的设备属性将使用关于设备的更多信息进行更新。 这将允许你创建条件性访问规则，用于从设备强制访问满足你的安全和合规性标准。 有关在 Microsoft Intune 中注册设备的详细信息，请参阅“在 Intune 中注册要管理的设备”。
Azure Active Directory 设备注册支持的方案：Azure Active Directory 设备注册包含对 iOS、Android 和 Windows 设备的支持。 利用 Azure AD 设备注册的各个方案可能有更具体的要求和平台支持。 

这些方案如下所述：

- **使用 Microsoft Intune 的 Office 365 应用程序的条件访问 ：**IT 管理员可以预配条件访问设备策略来保护公司资源，同时允许信息工作者在符合条件的设备上访问服务。 有关详细信息，请参阅“Office 365 服务的条件性访问设备策略”。

- **对在本地托管的应用程序的条件访问：**可以将已注册的设备与配置为将 AD FS 与 Windows Server 2012 R2 一起使用的应用程序的访问策略一起使用。 有关设置本地条件访问的详细信息，请参阅 [使用 Azure Active Directory 设备注册设置本地条件访问](active-directory-device-registration-on-premises-setup.md)。

## <a name="setting-up-azure-active-directory-device-registration"></a>设置 Azure Active Directory 设备注册

若要设置设备注册，可以使用多个选项：

- 加入 Azure AD 域时，可以注册设备。 有关本主题的详细信息，你可以详细了解 Azure AD 加入和用户加入 Azure AD 域所需的设置。

- 当用户将工作或学校帐户添加到个人设备上的 Windows 时，或者当移动设备连接到需要注册的工作资源时，可以注册设备。 若要确保此注册，必须在 Azure 门户中启用 Azure AD 设备注册。 

- 可以使用适用于已加入域的传统计算机的自动设备注册来注册设备。 若要确保此注册，必须先安装 Azure AD Connect，然后再继续自动设备注册。

有关最新说明，请参阅[如何使用 Azure Active Directory 配置已加入 Windows 域的设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)。  
还可以使用 Azure Active Directory 中的管理员门户查看和启用或禁用已注册的设备。

## <a name="enable-the-azure-active-directory-device-registration-service"></a>启用 Azure Active Directory 设备注册服务

**若要启用 Azure Active Directory 设备注册服务，请执行以下操作：**

1.    以管理员身份登录到 Microsoft Azure 门户。

2.    在左窗格中选择“Active Directory”。

3.    在“目录”选项卡中选择你的目录。

4.    单击 **“配置”**。

5.    滚动到“设备”。

6.    为用户选择“全部”可以将其设备注册到 AZURE AD 中。

7.    选择你想要按每个用户授权的设备的最大数目。

注册 Microsoft Intune 或适用于 Office 365 的移动设备管理需要进行设备注册。 如果已配置其中的任一服务，则会选中“全部”并禁用“无”。 请确保这些项正确配置，并且具有相应的许可。

默认情况下，不会为该服务启用双重身份验证。 但是，在注册设备时，建议启用双重身份验证。

- 在为该服务请求双重身份验证之前，你必须在 Azure Active Directory 中配置一个双重身份验证提供程序并为你的用户帐户配置 Multi-Factor Authentication，请参阅“将多重身份验证添加到 Azure Active Directory”

- 如果你将 AD FS 与 Windows Server 2012 R2 一起使用，则必须在 AD FS 中配置一个双因素身份验证模块，请参阅“将多重身份验证与 Active Directory 联合身份验证服务配合使用”。

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>查看和管理 Azure Active Directory 中的设备对象

在 Azure 管理员门户中，你可以查看、阻止和解除阻止设备。 被阻止的设备不再能够访问配置为只允许已注册设备访问的应用程序。

**若要查看和管理 Azure Active Directory 中的设备对象，请执行以下操作：**
 
1.    以管理员身份登录到 Microsoft Azure 门户。

2.    在左窗格中选择“Active Directory”。

3.    选择你的目录。

4.    选择“用户”。 

5.  单击要查看其设备的用户。

6.    选择“设备”。

7.    选择“已注册的设备”。

现在，你可以查看、阻止或取消阻止用户的已注册设备。
已加入本地域并自动注册的 Windows 10 设备不会显示在“用户”选项卡下。 请使用 Get-MsolDevice PowerShell 命令查找你企业中的所有设备。 


## <a name="next-steps"></a>后续步骤

若要设置自动设备注册，请参阅[如何使用 Azure Active Directory 配置已加入 Windows 域的设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)。




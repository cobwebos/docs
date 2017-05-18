---
title: "Office 365 服务的条件性访问设备策略 | Microsoft Docs"
description: "详细说明基于设备的条件如何控制对 Office 365 服务访问。 尽管信息工作者 (IW) 希望在工作中或在学校时从其个人设备访问 Office 365 服务（如 Exchange 和 SharePoint Online），但是其 IT 管理员希望访问是安全的。IT 管理员可以设置条件性访问设备策略来保护公司资源，同时允许 IW 在符合条件的设备上访问服务。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 613e2447c1e47fcd80f2e9ded9ad2669f8283c3d
ms.contentlocale: zh-cn
ms.lasthandoff: 12/29/2016


---
# <a name="conditional-access-device-policies-for-office-365-services"></a>Office 365 服务的条件性访问设备策略
“条件性访问”一词具有与之关联的多个条件，例如，通过多重身份验证的用户、通过身份验证的设备、符合标准的设备，等等。本主题主要介绍用于控制对 Office 365 服务访问的基于设备的条件性访问策略。 尽管信息工作者 (IW) 希望在工作中或在学校时从其个人设备访问 Office 365 服务（如 Exchange 和 SharePoint Online），但是其 IT 管理员希望访问是安全的。 IT 管理员可以设置条件性访问设备策略来保护公司资源，同时允许 IW 在符合条件的设备上访问服务。 可以从 Intune 条件性访问门户配置 Office 365 的条件性访问策略。

Azure Active Directory 会强制实施条件性访问策略来确保对 Office 365 服务的安全访问。 租户管理员可以创建一个条件性访问策略来阻止用户从不符合条件的设备访问 O365 服务。 用户必须符合公司的设备策略，才能向其授予对该服务的访问权限。 另外，管理员还可以创建策略来要求用户注册其设备来获取对 O365 服务的访问权限。 可以将策略应用于组织的所有用户，也可以先将其限定于少数目标组并随时间逐步增强以包括更多目标组。

对于用户而言，强制实施设备策略的一个先决条件是向 Azure Active Directory 设备注册服务注册其设备。 向 Azure Active Directory 设备注册服务注册设备时你可以选择启用多重身份验证 (MFA)。 建议为 Azure Active Directory 设备注册服务使用 MFA。 当启用了 MFA 时，向 Azure Active Directory 设备注册服务注册其设备的用户将面临第二因素身份验证的挑战。

## <a name="how-does-conditional-access-policy-work"></a>条件性访问策略的工作原理
当用户从受支持的设备平台请求访问 O365 服务时，Azure Active Directory 会对用户以及用户从中发起请求的设备进行身份验证；仅当用户符合为该服务设置的策略时，才会向其授予对该服务的访问权限。 对于没有注册其设备的用户，会向其提供补救说明来指示如何进行注册并满足访问公司 O365 服务的条件。 对于 iOS 和 Android 设备上的用户，将要求其使用公司门户应用程序注册其设备。 当用户注册其设备时，将向 Azure Active Directory 注册设备，并且针对设备管理和合规性进行注册。 客户必须将 Azure Active Directory 设备注册服务与 Microsoft Intune 一起使用才能为 Office 365 服务启用移动设备管理。 当强制实施了设备策略时，对于要访问 Office 365 服务的用户，进行设备注册是一个先决条件。

当用户成功注册了其设备时，设备将成为受信任设备。 Azure Active Directory 提供了单点登录来访问公司应用程序并强制实施条件性访问策略来授予对服务的访问权限，不仅在用户首次请求访问权限时如此，用户每次请求续订访问权限时都是如此。 如果登录凭据被更改、设备丢失/被盗或者在请求续订时不满足策略要求，则会拒绝用户访问服务。

## <a name="deployment-considerations"></a>部署注意事项：
必须使用 Azure Active Directory 设备注册服务进行设备注册。

当用户要在本地进行身份验证时，需要 Active Directory 联合身份验证服务 (AD FS)（1.0 及更高版本）。 当标识提供者不支持多重身份验证时，针对工作区加入的多重身份验证将失败。 例如，AD FS 2.0 不支持多重身份验证。 在 Azure Active Directory 设备注册服务上启用 MFA 之前，租户管理员必须确保本地 AD FS 支持 MFA 并确保启用了有效的 MFA 方法。 例如，Windows Server 2012 R2 上的 AD FS 具有 MFA 功能。 在 Azure Active Directory 设备注册服务上启用 MFA 之前，还必须在 AD FS 服务器上启用一种额外的有效身份验证 (MFA) 方法。 有关 AD FS 中受支持的 MFA 方法的更多详细信息，请参阅“为 AD FS 配置额外的身份验证方法”。

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)
问：针对不受支持的设备平台的默认排除策略是什么？

答：目前，对于 iOS 和 Android 设备上的用户，会有选择地强制实施条件性访问策略。 默认情况下，其他设备平台上的应用程序不受针对 iOS 和 Android 设备的条件性访问策略的影响。 但是，租户管理员可以选择覆盖全局策略来禁止不受支持的平台上的用户进行访问。
目前正在计划将条件性访问策略扩展到其他平台（包括 Windows）上的用户。

问：何时会将对 Office 365 服务的条件性访问策略扩展到基于浏览器的应用程序（例如，OWA、基于浏览器的 SharePoint）？

答：目前，对 Office365 服务的条件性访问仅限于设备上的丰富应用程序。 目前正在计划将条件性访问策略扩展到从浏览器访问服务的用户。



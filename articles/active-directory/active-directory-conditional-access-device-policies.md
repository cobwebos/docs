---
title: "Office 365 服务的 Azure Active Directory 条件访问设备策略 | Microsoft Docs"
description: "了解如何设置条件访问设备策略，从而增强企业资源的安全性，同时保持用户符合性和对服务的访问权限。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d33babd331570624ca6a0fc967c79dbc467a1b40
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Office 365 服务的 Active Directory 条件访问设备策略

需要完成多个步骤才能使条件访问正常工作。 它涉及经过多重身份验证的用户、经身份验证的设备和兼容设备等因素。 本文主要关注基于设备的条件，你的组织可以使用这些条件来帮助你控制对 Office 365 服务的访问权限。 

企业用户想要在工作中或在学校时从其个人设备访问 Office 365 服务（如 Exchange 和 SharePoint Online）。 你会希望确保访问的安全性。 可以设置条件访问设备策略以增强企业资源的安全性，同时授予使用兼容设备的用户对服务的访问权限。 可以在 Microsoft Intune 条件访问门户中设置 Office 365 的条件访问策略。

Azure Active Directory (Azure AD) 强制实施条件访问策略，以帮助确保对 Office 365 服务的安全访问。 你可以创建一个条件访问策略来阻止使用非兼容设备的用户对 Office 365 服务的访问。 用户必须符合公司的设备策略，才能向其授予对该服务的访问权限。 另外，你还可以创建策略来要求用户注册其设备，以获取对 Office 365 服务的访问权限。 可将策略应用于组织中的所有用户，或限定于少数目标组。 随着时间的推移，可以将多个目标组添加到策略。

强制实施设备策略的一个先决条件是，用户必须向 Azure AD 设备注册服务注册其设备。 你可以选择启用注册了 Azure AD 设备注册服务的设备的多重身份验证。 建议针对 Azure Active Directory 设备注册服务使用多重身份验证。 启用多重身份验证后，向 Azure AD 设备注册服务注册其设备的用户将面临第二重身份验证。

## <a name="how-does-a-conditional-access-policy-work"></a>条件访问策略是如何工作的？

当用户请求从受支持的设备平台访问 Office 365 服务时，Azure AD 会对用户和设备进行身份验证。 仅当用户符合为该服务设置的策略时，Azure AD 才会授予其对服务的访问权限。 对于未经注册的设备上的用户，会向其提供说明，指示如何进行注册并满足访问企业 Office 365 服务的条件。 对于 iOS 和 Android 设备上的用户，会要求其使用 Intune 公司门户应用程序注册其设备。 当用户注册一台设备时，将向 Azure AD 注册设备，并针对设备管理和符合性进行注册。 必须将 Azure AD 设备注册服务与 Microsoft Intune 一起使用才能为 Office 365 服务启用移动设备管理。 强制实施设备策略时，用户需要设备注册才能访问 Office 365 服务。

当用户成功注册设备时，该设备将成为受信任设备。 Azure AD 为经过身份验证的用户提供对公司应用程序的单一登录访问权限。 Azure AD 通过强制实施条件访问策略授予用户对服务的访问权限，这不限于首次请求访问，而且每次用户续订访问请求时都可以进行访问。 如果登录凭据变更、设备丢失或被盗或者在请求续订时不满足策略条件要求，则会拒绝用户对服务的访问。

## <a name="deployment-considerations"></a>部署注意事项

必须使用 Azure AD 设备注册服务来注册设备。

当本地用户要进行身份验证时，需要 Active Directory 联合身份验证服务 (AD FS)（1.0 版以及更高版本）。 当标识提供者不支持多重身份验证时，针对 Workplace Join 的多重身份验证将失败。 例如，不能将多重身份验证与 AD FS 2.0 一起使用。 在启用 Azure AD 设备注册服务的多重身份验证之前，确保本地 AD FS 与多重身份验证配合使用，并且有效的多重身份验证方法已准备就绪。 例如，Windows Server 2012 R2 上的 AD FS 具有多重身份验证功能。 在启用 Azure AD 设备注册服务的多重身份验证之前，还必须在 AD FS 服务器上设置其他有效的身份验证（多重身份验证）方法。 有关 AD FS 中受支持的多重身份验证方法的详细信息，请参阅[为 AD FS 配置额外的身份验证方法](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)。

## <a name="next-steps"></a>后续步骤

*   对于常见问题解答，请参阅 [Azure Active Directory 条件访问常见问题解答](active-directory-conditional-faqs.md)。

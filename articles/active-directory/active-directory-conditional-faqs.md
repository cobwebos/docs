---
title: "Azure Active Directory 条件访问常见问题解答 | Microsoft Docs"
description: "获取有关 Azure Active Directory 中条件访问的常见问题的解答。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5eb16a92feee0937edeff1c00e0babe85176a79f
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory 条件访问常见问题解答

## <a name="which-applications-work-with-conditional-access-policies"></a>哪些应用程序使用条件性访问策略？

有关与条件访问策略配合使用的应用程序的信息，请参阅[使用 Azure Active Directory 中条件访问规则的应用程序和浏览器](active-directory-conditional-access-supported-apps.md)。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>是否对 B2B 协作用户和来宾用户执行条件性访问策略？

对企业到企业 (B2B) 协作用户执行策略。 但在某些情况下，用户可能无法满足策略要求。 例如，来宾用户的组织可能不支持多重身份验证。 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 策略是否也适用于 OneDrive for Business？

是的。 SharePoint Online 策略也适用于 OneDrive for Business。


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>为何无法对诸如 Word 或 Outlook 等客户端应用设置策略？

条件访问策略设置访问服务的要求。 它在对该服务执行身份验证时执行。 不在客户端应用程序上直接设置策略。 相反，在客户端调用服务时应用策略。 例如，在 SharePoint 上设置的策略适用于调用 SharePoint 的客户端。 在 Exchange 上设置的策略适用于 Outlook。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>条件性访问策略是否应用于服务帐户？

条件访问策略适用于所有服务帐户， 其中包括用作服务帐户的用户帐户。 通常情况下，运行时无人参与的服务帐户无法满足条件访问策略的要求。 例如，可能需要多重身份验证。 可使用条件访问策略管理设置从策略中排除服务帐户。 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>图形 API 是否可用于配置条件访问策略？

目前不可以。 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>针对不受支持的设备平台的默认排除策略是什么？

目前，对于 iOS 和 Android 设备用户，会有选择地执行条件访问策略。 默认情况下，其他设备平台上的应用程序不受针对 iOS 和 Android 设备的条件访问策略的影响。 租户管理员可选择替代全局策略，禁止不受支持平台上的用户进行访问。


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>条件访问策略如何用于 Microsoft Teams？

Microsoft Teams 的核心工作效率方案严重依赖 Exchange Online 和 SharePoint Online，例如会议、日历和文件共享。 用户直接登录到 Microsoft Teams 时，为这些云应用设置的条件访问策略会应用于 Microsoft Teams。

Microsoft Teams 还在 Azure Active Directory 条件访问策略中作为云应用单独受到支持。 用户登录时，为云应用设置的条件访问策略会应用于 Microsoft Teams。 然而，在没有对其他应用（如 Exchange Online 和 SharePoint Online）实施正确策略的情况下，用户仍可能能够直接访问这些资源。

适用于 Windows 和 Mac 的 Microsoft Teams 桌面客户端支持新式身份验证。 新式身份验证将基于 Azure Active Directory 身份验证库 (ADAL) 的登录引入 Microsoft Office 客户端应用程序。
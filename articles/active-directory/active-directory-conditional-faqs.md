---
title: "Azure Active Directory 条件性访问 FAQ | Microsoft Docs"
description: "有关条件性访问的常见问题 "
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
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3980b99206c9f0c81ba3d03778777878e25db7c8
ms.lasthandoff: 12/29/2016


---
# <a name="azure-active-directory-conditional-access-faq"></a>Azure Active Directory 条件性访问 FAQ
## <a name="which-applications-work-with-conditional-access-policies"></a>哪些应用程序使用条件性访问策略？
**答：**请参阅主题[条件性访问 — 支持哪些应用程序](active-directory-conditional-access-supported-apps.md)。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>是否对 B2B 协作用户和来宾用户执行条件性访问策略？
**答：**对 B2B 协作用户执行这些策略。 但是，在某些情况下，用户可能无法满足策略要求，例如，如果组织不支持多重身份验证。 

当前不对 SharePoint 来宾用户执行此策略。 来宾关系在 SharePoint 中维护。 来宾用户帐户不受身份验证服务器上访问策略的限制。 来宾访问可以在 SharePoint 上进行管理

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 策略是否也适用于 OneDrive for Business？
**答：**是。

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>为何无法对诸如 Word 或 Outlook 等客户端应用设置策略？
**答：**条件性访问策略为访问服务设置要求，当服务发生身份验证时执行该策略。 不对客户端应用直接设置策略，而是在客户端应用调用服务时应用策略。 例如，对 SharePoint 设置的策略可应用于调用 SharePoint 的客户端，对 Exchange 设置的策略可应用于 Outlook。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>条件性访问策略是否应用于服务帐户？
**答：**条件性访问策略应用于所有服务帐户。 其中包括用作服务帐户的用户帐户。 在许多情况下，以无人参与方式运行的服务帐户无法满足策略。 例如，在需要进行 MFA 的情况下。 在这些情况下，可以使用条件性访问策略管理设置从策略中排除服务帐户。 在此处了解更多有关将策略应用于用户的信息。



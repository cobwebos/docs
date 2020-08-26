---
title: Azure Active Directory 条件访问常见问题解答 | Microsoft Docs
description: 获取有关 Azure Active Directory 中条件访问的常见问题的解答。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5e75f7214e3a9aa5b44a763cc3448583b47185f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253233"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory 条件访问常见问题解答

## <a name="which-applications-work-with-conditional-access-policies"></a>哪些应用程序使用条件访问策略？

有关使用条件访问策略的应用程序的信息，请参阅[使用 Azure Active Directory 中条件访问规则的应用程序和浏览器](concept-conditional-access-cloud-apps.md)。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>是否对 B2B 协作用户和来宾用户强制实施条件访问策略？

对企业到企业 (B2B) 协作用户执行策略。 但在某些情况下，用户可能无法满足策略要求。 例如，来宾用户的组织可能不支持多重身份验证。 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 策略是否也适用于 OneDrive for Business？

是的。 SharePoint Online 策略也适用于 OneDrive for Business。 有关详细信息，请参阅文章：[条件访问服务依赖项](service-dependencies.md)，并考虑将策略定向到[Office 365 应用](concept-conditional-access-cloud-apps.md#office-365-preview)。

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>为何无法对 Word 或 Outlook 之类的客户端应用直接设置策略？

条件访问策略设置访问服务的要求。 它在对该服务执行身份验证时执行。 不在客户端应用程序上直接设置策略。 相反，在客户端调用服务时应用策略。 例如，在 SharePoint 上设置的策略适用于调用 SharePoint 的客户端。 在 Exchange 上设置的策略适用于 Outlook。 有关详细信息，请参阅文章：[条件访问服务依赖项](service-dependencies.md)，并考虑将策略定向到[Office 365 应用](concept-conditional-access-cloud-apps.md#office-365-preview)。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>条件访问策略是否适用于服务帐户？

条件访问策略适用于所有用户帐户。 其中包括用作服务帐户的用户帐户。 通常，在无人参与模式下运行的服务帐户无法满足条件访问策略的要求。 例如，可能需要多重身份验证。 可以使用[用户或组排除](concept-conditional-access-users-groups.md#exclude-users)从策略中排除服务帐户。 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>针对不受支持的设备平台的默认排除策略是什么？

目前，对于 iOS 和 Android 设备用户，会有选择地强制实施条件访问策略。 默认情况下，其他设备平台上的应用程序不受针对 iOS 和 Android 设备的条件访问策略的影响。 租户管理员可选择替代全局策略，禁止不受支持平台上的用户进行访问。

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>条件访问策略如何用于 Microsoft Teams？

Microsoft Teams 的核心工作效率方案严重依赖 Exchange Online 和 SharePoint Online，例如会议、日历和文件共享。 用户直接登录到 Microsoft Teams 时，为这些云应用设置的条件访问策略会应用于 Microsoft Teams。

在条件访问策略中，Microsoft 团队也作为云应用单独受到支持。 用户登录时，为云应用设置的条件访问策略会应用于 Microsoft Teams。 然而，在没有对其他应用（如 Exchange Online 和 SharePoint Online）实施正确策略的情况下，用户仍可能能够直接访问这些资源。

适用于 Windows 和 Mac 的 Microsoft Teams 桌面客户端支持新式身份验证。 新式身份验证将基于 Azure Active Directory 身份验证库 (ADAL) 的登录引入 Microsoft Office 客户端应用程序。

有关详细信息，请参阅文章：[条件访问服务依赖项](service-dependencies.md)，并考虑将策略定向到[Office 365 应用](concept-conditional-access-cloud-apps.md#office-365-preview)。

## <a name="why-are-some-tabs-not-working-in-microsoft-teams-after-enabling-conditional-access-policies"></a>为什么在启用条件访问策略后，某些选项卡不能在 Microsoft 团队中工作？

在 Microsoft 团队中的租户上启用某些条件性访问策略后，某些选项卡可能在桌面客户端中无法正常运行。 但是，在使用 Microsoft 团队 web 客户端时，受影响的选项卡功能。 受影响的选项卡可能包括 Power BI、表单、VSTS、PowerApps 和 SharePoint 列表。

若要查看受影响的选项卡，必须在安装了 Windows 10 帐户扩展的边缘、Internet Explorer 或 Chrome 中使用团队 web 客户端。 某些选项卡依赖于 web 身份验证，当启用条件访问时，它不会在 Microsoft 团队桌面客户端中工作。 Microsoft 与合作伙伴合作来实现这些方案。 到目前为止，我们启用了涉及 Planner、OneNote 和流的 scenrarios。

## <a name="next-steps"></a>后续步骤

- 若要为环境配置条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](best-practices.md)。 

---
title: 使用条件访问的应用保护策略 - Azure Active Directory
description: 了解如何要求应用保护策略通过 Azure Active Directory 中的条件访问来访问云应用。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/08/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4476502896705c2133b09b203bea0d6f5d74f121
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681414"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>如何：要求应用保护策略和核准的客户端设备通过条件访问来访问云应用

员工定期使用其移动设备执行个人和工作任务。 在确保员工能够高效工作的同时，组织还需要防止可能不安全的应用程序中的数据丢失。 使用条件访问，组织可以限制对经批准（支持新式身份验证）的客户端应用的访问，这些客户端应用采用了 Intune 应用保护策略。

本文介绍三种方案，用于为 Office 365、Exchange Online 和 SharePoint Online 等资源配置条件访问策略。

- [方案 1：Office 365 应用需要经批准的具有应用保护策略的应用](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [方案 2：浏览器应用需要经批准的具有应用保护策略的应用](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [方案 3：Exchange Online 和 SharePoint Online 需要经批准的客户端应用和应用保护策略](#scenario-3-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

在条件访问中，这些客户端应用被认为是使用应用保护策略进行保护。 有关应用保护策略的详细信息，请参阅文章[应用保护策略概述](/intune/apps/app-protection-policy)

有关符合条件的客户端应用的列表，请参阅[应用保护策略要求](concept-conditional-access-grant.md)。

> [!NOTE]
>    or 子句用于策略中，使用户能够利用支持“需要应用保护策略”或“需要批准的客户端应用”授权控件的应用 。 若要详细了解哪些应用支持“需要应用保护策略”授权控件，请参阅[应用保护策略要求](concept-conditional-access-grant.md)。

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>应用场景 1：Office 365 应用需要已批准的具有应用保护策略的应用

在此方案中，Contoso 已决定，在获得访问权限之前，对 Office 365 资源的所有移动访问都必须使用经批准的客户端应用，如 Outlook Mobile、OneDrive 和 Microsoft Teams，这些应用受应用保护策略保护。 其所有用户已使用 Azure AD 凭据登录，并拥有分配的许可证（包括 Azure AD Premium P1 或 P2 和 Microsoft Intune）。

组织必须完成以下步骤才能在移动设备上要求使用经批准的客户端应用。

**步骤 1：为 Office 365 配置 Azure AD 条件访问策略**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure 门户。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择要将此策略应用到的“所有用户”或特定“用户和组”  。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“Office 365 (预览版)”。
1. 在“条件”下，选择“设备平台”。
   1. 将“配置”设置为“是” 。
   1. 包括“Android”和“iOS”。 
1. 在“条件”下，选择“客户端应用（预览版）”。
   1. 将“配置”设置为“是” 。
   1. 选择“移动应用和桌面客户端”以及“新式身份验证客户端” 。
1. 在“访问控制” > “授予”下，选择以下选项：
   - 需要经批准的客户端应用
   - 需要应用保护策略（预览版）
   - ”需要所有已选控件“
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”进行创建并启用策略。

**步骤 2：为 Exchange Online with ActiveSync (EAS) 配置 Azure AD 条件访问策略**

对于此步骤中的条件访问策略，配置以下组件：

1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择要将此策略应用到的“所有用户”或特定“用户和组”  。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“Office 365 Exchange Online”。
1. 在“条件”下：
   1. 客户端应用（预览版）：
      1. 将“配置”设置为“是” 。
      1. 选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要应用保护策略”和“选择”。
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”进行创建并启用策略。

**步骤 3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

有关为 Android 和 iOS 创建应用保护策略的步骤，请查看[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文。 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>应用场景 2：浏览器应用需要经批准的具有应用保护策略的应用

在此方案中，Contoso 已决定，在获得访问权限之前，对 Office 365 资源的所有移动访问都必须使用经批准的客户端应用，如适用于 iOS 和 Android 的 Edge，这些应用受应用保护策略保护。 其所有用户已使用 Azure AD 凭据登录，并拥有分配的许可证（包括 Azure AD Premium P1 或 P2 和 Microsoft Intune）。

组织必须完成以下步骤才能在移动设备上要求使用经批准的客户端应用。

**步骤 1：为 Office 365 配置 Azure AD 条件访问策略**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure 门户。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择要将此策略应用到的“所有用户”或特定“用户和组”  。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“Office 365 (预览版)”。
1. 在“条件”下，选择“设备平台”。
   1. 将“配置”设置为“是” 。
   1. 包括“Android”和“iOS”。 
1. 在“条件”下，选择“客户端应用（预览版）”。
   1. 将“配置”设置为“是” 。
   1. 选择“浏览器”。
1. 在“访问控制” > “授予”下，选择以下选项：
   - 需要经批准的客户端应用
   - 需要应用保护策略（预览版）
   - ”需要所有已选控件“
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”进行创建并启用策略。

**步骤 2：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

有关为 Android 和 iOS 创建应用保护策略的步骤，请查看[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文。 

## <a name="scenario-3-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>应用场景 3：Exchange Online 和 SharePoint Online 需要经批准的客户端应用和应用保护策略

在此方案中，Contoso 已决定，用户在移动设备上只能访问电子邮件和 SharePoint 数据，前提是，他们在获得访问权限之前使用经批准的受应用保护策略保护的客户端应用（如 Outlook Mobile）。 其所有用户已使用 Azure AD 凭据登录，并拥有分配的许可证（包括 Azure AD Premium P1 或 P2 和 Microsoft Intune）。

组织必须完成以下三个步骤才能要求在移动设备和 Exchange ActiveSync 客户端上使用经批准的客户端应用。

**步骤 1：在访问 Exchange Online 和 SharePoint Online 时，基于 Android 和 iOS 的新式身份验证客户端的策略要求使用已批准的客户端应用和应用保护策略。**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure 门户。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择要将此策略应用到的“所有用户”或特定“用户和组”  。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“Office 365 Exchange Online”和“Office 365 SharePoint Online”   。
1. 在“条件”下，选择“设备平台”。
   1. 将“配置”设置为“是” 。
   1. 包括“Android”和“iOS”。 
1. 在“条件”下，选择“客户端应用（预览版）”。
   1. 将“配置”设置为“是” 。
   1. 选择“移动应用和桌面客户端”以及“新式身份验证客户端” 。
1. 在“访问控制” > “授予”下，选择以下选项：
   - 需要经批准的客户端应用
   - 需要应用保护策略（预览版）
   - “需要某一已选控件”
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”进行创建并启用策略。

**步骤 2：要求使用核准的客户端应用的 Exchange ActiveSync 客户端的策略。**

1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择要将此策略应用到的“所有用户”或特定“用户和组”  。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“Office 365 Exchange Online”。
1. 在“条件”下：
   1. 客户端应用（预览版）：
      1. 将“配置”设置为“是” 。
      1. 选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要应用保护策略”和“选择”。
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”进行创建并启用策略。

**步骤 3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**。

有关为 Android 和 iOS 创建应用保护策略的步骤，请查看[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文。 

## <a name="next-steps"></a>后续步骤

[什么是条件访问？](overview.md)

[条件访问组件](concept-conditional-access-policies.md)

[常用条件访问策略](concept-conditional-access-policy-common.md)


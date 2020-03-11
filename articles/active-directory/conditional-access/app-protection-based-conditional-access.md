---
title: 具有条件访问的应用保护策略-Azure Active Directory
description: 了解如何在 Azure Active Directory 中使用条件性访问来要求应用保护策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080067"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>如何：要求使用条件性访问的应用保护策略和批准的客户端应用进行云应用访问

人们会定期将他们的移动设备用于个人任务和工作任务。 在确保员工能够提高工作效率的同时，组织还需要防止可能不安全的应用程序的数据丢失。 使用条件性访问，组织可将访问权限限制为应用了 Intune 应用保护策略的已批准（支持新式身份验证）客户端应用。

本文介绍了两种方案，用于为 Office 365、Exchange Online 和 SharePoint Online 等资源配置条件访问策略。

- [方案1： Office 365 应用需要具有应用保护策略的已批准应用](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [方案2： Exchange Online 和 SharePoint Online 需要批准的客户端应用和应用保护策略](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

在条件性访问中，这些客户端应用被认为是使用应用保护策略保护的。 有关应用保护策略的详细信息，请参阅[应用保护策略概述](/intune/apps/app-protection-policy)

有关符合条件的客户端应用的列表，请参阅[应用保护策略要求](concept-conditional-access-grant.md)。

> [!NOTE]
>    在策略中使用或子句，以使用户能够利用支持 "**需要应用保护策略**" 或 "**需要批准的客户端应用**授予控制" 的应用。 有关哪些应用支持 "**需要应用保护策略**" 授权控制的详细信息，请参阅[应用保护策略要求](concept-conditional-access-grant.md)。

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>方案1： Office 365 应用需要具有应用保护策略的已批准应用

在此方案中，Contoso 已决定对 Office 365 资源的所有移动访问都必须使用批准的客户端应用（如 Outlook mobile、OneDrive 和 Microsoft 团队），才能接收访问权限。 其所有用户已使用 Azure AD 凭据登录，并向其分配了许可证，其中包括 Azure AD Premium P1 或 P2 和 Microsoft Intune。

组织必须完成以下步骤才能在移动设备上使用批准的客户端应用。

**步骤1：配置 Office 365 的 Azure AD 条件性访问策略**

1. 以全局管理员、安全管理员或条件访问管理员身份登录到**Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择要将此策略应用到的 "**所有用户**" 或特定**用户和组**。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择 " **Office 365 （预览版）** "。
1. 在 "**条件**" 下，选择 "**设备平台**"。
   1. 将 "**配置**" 设置为 **"是"** 。
   1. 包括**Android**和**iOS**。
1. 在 "**条件**" 下，选择 "**客户端应用（预览）** "。
   1. 将 "**配置**" 设置为 **"是"** 。
   1. 选择 "**移动应用和桌面客户端**和**新式身份验证客户端**"。
1. 在 "**访问控制**" > **Grant**"下，选择以下选项：
   - **需要批准的客户端应用**
   - **需要应用保护策略（预览）**
   - “需要某一已选控件”
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以创建和启用策略。

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略（EAS）**

对于此步骤中的条件性访问策略，请配置下列组件：

1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择要将此策略应用到的 "**所有用户**" 或特定**用户和组**。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择 " **Office 365 Exchange Online**"。
1. **条件**：
   1. **客户端应用（预览）** ：
      1. 将 "**配置**" 设置为 **"是"** 。
      1. 选择 "**移动应用和桌面客户端**和**Exchange ActiveSync 客户端**"。
1. 在 "**访问控制**" > **grant**"下，选择"**授予访问权限**，**需要应用保护策略**"，然后选择"**选择**"。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以创建和启用策略。

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

请参阅[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文，了解如何创建适用于 Android 和 iOS 的应用保护策略的步骤。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>方案2： Exchange Online 和 SharePoint Online 需要批准的客户端应用和应用保护策略

在此方案中，Contoso 已决定用户在移动设备上使用经过批准的客户端应用（例如，在接收访问权限之前由应用保护策略保护的 Outlook mobile），只可访问移动设备上的电子邮件和 SharePoint 数据。 其所有用户已使用 Azure AD 凭据登录，并向其分配了许可证，其中包括 Azure AD Premium P1 或 P2 和 Microsoft Intune。

组织必须完成以下三个步骤，才能要求在移动设备和 Exchange ActiveSync 客户端上使用已批准的客户端应用。

**步骤1：基于 Android 和 iOS 的新式身份验证客户端策略，在访问 Exchange Online 和 SharePoint Online 时需要使用已批准的客户端应用和应用保护策略。**

1. 以全局管理员、安全管理员或条件访问管理员身份登录到**Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择要将此策略应用到的 "**所有用户**" 或特定**用户和组**。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择**Office 365 Exchange Online**和**office 365 SharePoint online**。
1. 在 "**条件**" 下，选择 "**设备平台**"。
   1. 将 "**配置**" 设置为 **"是"** 。
   1. 包括**Android**和**iOS**。
1. 在 "**条件**" 下，选择 "**客户端应用（预览）** "。
   1. 将 "**配置**" 设置为 **"是"** 。
   1. 选择 "**移动应用和桌面客户端**和**新式身份验证客户端**"。
1. 在 "**访问控制**" > **Grant**"下，选择以下选项：
   - **需要批准的客户端应用**
   - **需要应用保护策略（预览）**
   - “需要某一已选控件”
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以创建和启用策略。

**步骤2：针对需要使用批准的客户端应用的 Exchange ActiveSync 客户端的策略。**

1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择要将此策略应用到的 "**所有用户**" 或特定**用户和组**。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择 " **Office 365 Exchange Online**"。
1. **条件**：
   1. **客户端应用（预览）** ：
      1. 将 "**配置**" 设置为 **"是"** 。
      1. 选择 "**移动应用和桌面客户端**和**Exchange ActiveSync 客户端**"。
1. 在 "**访问控制**" > **grant**"下，选择"**授予访问权限**，**需要应用保护策略**"，然后选择"**选择**"。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以创建和启用策略。

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略。**

请参阅[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文，了解如何创建适用于 Android 和 iOS 的应用保护策略的步骤。 

## <a name="next-steps"></a>后续步骤

[什么是条件性访问？](overview.md)

[条件访问组件](concept-conditional-access-policies.md)

[常见的条件访问策略](concept-conditional-access-policy-common.md)


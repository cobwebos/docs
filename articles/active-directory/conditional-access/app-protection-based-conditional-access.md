---
title: 使用条件访问的应用保护策略 - Azure Active Directory
description: 了解如何通过 Azure Active Directory 中的条件访问要求使用应用保护策略进行云应用访问。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631901"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>如何：需要应用保护策略和经批准的客户端应用才能通过条件访问访问云应用

人们经常使用他们的移动设备执行个人和工作任务。 在确保员工能够提高工作效率的同时，组织还希望防止潜在不安全应用程序的数据丢失。 使用条件访问，组织可以使用 Intune 应用保护策略限制对已批准的（支持现代身份验证）客户端应用的访问。

本文介绍了两种方案，用于为 Office 365、联机交换和联机共享点等资源配置条件访问策略。

- [方案 1：Office 365 应用需要具有应用保护策略的已批准应用](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [方案 2：在线交换和共享点联机需要经过批准的客户端应用和应用保护策略](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

在条件访问中，这些客户端应用已知受应用保护策略的保护。 有关应用保护策略的详细信息，请参阅文章"[应用保护策略概述](/intune/apps/app-protection-policy)"

有关符合条件的客户端应用的列表，请参阅[应用保护策略要求](concept-conditional-access-grant.md)。

> [!NOTE]
>    在策略中使用 或 子句，使用户能够利用支持 **"需要应用保护策略**"或 **"需要已批准的客户端应用**授予"控件的应用。 有关哪些应用支持"**需要应用保护策略**授予控制"的详细信息，请参阅[应用保护策略要求](concept-conditional-access-grant.md)。

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>方案 1：Office 365 应用需要具有应用保护策略的已批准应用

在此方案中，Contoso 决定所有对 Office 365 资源的移动访问都必须使用已批准的客户端应用，如 Outlook 移动、OneDrive 和 Microsoft 团队，在接收访问权限之前，受应用保护策略保护。 其所有用户都已使用 Azure AD 凭据登录，并已为其分配了许可证，其中包括 Azure AD 高级 P1 或 P2 和 Microsoft Intune。

组织必须完成以下步骤，才能要求在移动设备上使用经批准的客户端应用。

**步骤 1：为 Office 365 配置 Azure AD 条件访问策略**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在 **"分配"** 下，选择 **"用户"和"组**"
   1. 在 **"包括**"下，选择"**所有用户**"或要应用此策略的特定**用户和组**。 
   1. 选择 **"完成**"。
1. 在 **"云应用"或"** > **操作包括**"下，选择**Office 365（预览版）。**
1. 在 **"条件"下**，选择**设备平台**。
   1. 将 **"配置"** 设置为 **"是**"。
   1. 包括**安卓**和**iOS。**
1. 在 **"条件"下**，选择**客户端应用（预览）。**
   1. 将 **"配置"** 设置为 **"是**"。
   1. 选择“移动应用和桌面客户端”**** 和“新式身份验证客户端”****。
1. 在 **"访问控制** > **授予"下**，选择以下选项：
   - **需要批准的客户端应用**
   - **需要应用保护策略（预览版）**
   - ”需要所有已选控件“****
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**第 2 步：配置 Azure AD 条件访问策略，以便与 ActiveSync （EAS） 联机交换**

对于此步骤中的条件访问策略，请配置以下组件：

1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在 **"分配"** 下，选择 **"用户"和"组**"
   1. 在 **"包括**"下，选择"**所有用户**"或要应用此策略的特定**用户和组**。 
   1. 选择 **"完成**"。
1. 在 **"云"应用或操作** > **包括**下，选择**Office 365 在线交换**。
1. 在**条件**下 ：
   1. **客户端应用（预览）：**
      1. 将 **"配置"** 设置为 **"是**"。
      1. 选择**移动应用和桌面客户端**，并**交换 ActiveSync 客户端**。
1. 在 **"访问控制** > **授予"下**，选择 **"授予访问权限**"，"**需要应用保护策略**"，然后选择 **"选择**"。
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**第 3 步：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

查看文章["如何创建和分配应用保护策略](/intune/apps/app-protection-policies)，了解为 Android 和 iOS 创建应用保护策略的步骤。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>方案 2：在线交换和共享点联机需要经过批准的客户端应用和应用保护策略

在这种情况下，Contoso 决定，用户只能访问移动设备上的电子邮件和 SharePoint 数据，只要他们在接收访问权限之前使用受应用保护策略保护的已批准客户端应用（如 Outlook Mobile）。 其所有用户都已使用 Azure AD 凭据登录，并已为其分配了许可证，其中包括 Azure AD 高级 P1 或 P2 和 Microsoft Intune。

组织必须完成以下三个步骤，才能要求在移动设备和 Exchange ActiveSync 客户端上使用经批准的客户端应用。

**第 1 步：基于 Android 和 iOS 的现代身份验证客户端的策略，在访问在线 Exchange 和 SharePoint 在线时需要使用经批准的客户端应用和应用保护策略。**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在 **"分配"** 下，选择 **"用户"和"组**"
   1. 在 **"包括**"下，选择"**所有用户**"或要应用此策略的特定**用户和组**。 
   1. 选择 **"完成**"。
1. 在 **"云应用"或"操作** > **包括**"下，选择**Office 365 在线交换**和**Office 365 在线共享点**。
1. 在 **"条件"下**，选择**设备平台**。
   1. 将 **"配置"** 设置为 **"是**"。
   1. 包括**安卓**和**iOS。**
1. 在 **"条件"下**，选择**客户端应用（预览）。**
   1. 将 **"配置"** 设置为 **"是**"。
   1. 选择“移动应用和桌面客户端”**** 和“新式身份验证客户端”****。
1. 在 **"访问控制** > **授予"下**，选择以下选项：
   - **需要批准的客户端应用**
   - **需要应用保护策略（预览版）**
   - “需要某一已选控件”****
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**步骤 2：需要使用已批准客户端应用的 Exchange ActiveSync 客户端的策略。**

1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在 **"分配"** 下，选择 **"用户"和"组**"
   1. 在 **"包括**"下，选择"**所有用户**"或要应用此策略的特定**用户和组**。 
   1. 选择 **"完成**"。
1. 在 **"云"应用或操作** > **包括**下，选择**Office 365 在线交换**。
1. 在**条件**下 ：
   1. **客户端应用（预览）：**
      1. 将 **"配置"** 设置为 **"是**"。
      1. 选择**移动应用和桌面客户端**，并**交换 ActiveSync 客户端**。
1. 在 **"访问控制** > **授予"下**，选择 **"授予访问权限**"，"**需要应用保护策略**"，然后选择 **"选择**"。
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**第 3 步：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略。**

查看文章["如何创建和分配应用保护策略](/intune/apps/app-protection-policies)，了解为 Android 和 iOS 创建应用保护策略的步骤。 

## <a name="next-steps"></a>后续步骤

[什么是条件访问？](overview.md)

[条件访问组件](concept-conditional-access-policies.md)

[常用条件访问策略](concept-conditional-access-policy-common.md)


---
title: 使用条件访问的已批准客户端应用 - Azure Active Directory
description: 了解如何使用 Azure Active Directory 中的条件访问要求使用经批准的客户端应用进行云应用访问。
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
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480889"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>如何：需要经过批准的客户端应用才能通过条件访问访问云应用

人们经常使用他们的移动设备执行个人和工作任务。 在确保员工能够提高工作效率的同时，组织还希望防止潜在不安全应用程序的数据丢失。 使用条件访问，组织可以限制对已批准（支持现代身份验证）客户端应用的访问。

本文介绍了两种方案，用于为 Office 365、联机交换和联机共享点等资源配置条件访问策略。

- [方案 1：Office 365 应用需要经过批准的客户端应用](#scenario-1-office-365-apps-require-an-approved-client-app)
- [方案 2：在线交换和共享点联机需要经过批准的客户端应用](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

在条件访问中，此功能称为需要批准的客户端应用。 有关核准客户端应用程序的列表，请参阅[核准客户端应用程序要求](concept-conditional-access-grant.md#require-approved-client-app)。

> [!NOTE]
> 为了需要 iOS 和 Android 设备的已批准客户端应用，这些设备必须首先在 Azure AD 中注册。

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>方案 1：Office 365 应用需要经过批准的客户端应用

在此方案中，Contoso 决定使用移动设备的用户只要使用经批准的客户端应用（如 Outlook 移动、OneDrive 和 Microsoft Teams），即可访问所有 Office 365 服务。 其所有用户都已使用 Azure AD 凭据登录，并已为其分配了许可证，其中包括 Azure AD 高级 P1 或 P2 和 Microsoft Intune。

组织必须完成以下三个步骤，才能要求在移动设备上使用经批准的客户端应用。

**第 1 步：基于 Android 和 iOS 的现代身份验证客户端的策略，在访问在线 Exchange 时需要使用经批准的客户端应用程序。**

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
1. 在 **"访问控制** > **授予"** 下，选择 **"授予访问权限****"，"需要已批准的客户端应用**"，然后选择 **"选择**"。
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**第 2 步：配置 Azure AD 条件访问策略，以便与 ActiveSync （EAS） 联机交换**

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
1. 在 **"访问控制** > **授予"** 下，选择 **"授予访问权限****"，"需要已批准的客户端应用**"，然后选择 **"选择**"。
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**第 3 步：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略。**

查看文章["如何创建和分配应用保护策略](/intune/apps/app-protection-policies)，了解为 Android 和 iOS 创建应用保护策略的步骤。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>方案 2：在线交换和共享点联机需要经过批准的客户端应用

在这种情况下，Contoso 决定用户只要使用已批准的客户端应用（如 Outlook 移动），他们只能访问移动设备上的电子邮件和 SharePoint 数据。 其所有用户都已使用 Azure AD 凭据登录，并已为其分配了许可证，其中包括 Azure AD 高级 P1 或 P2 和 Microsoft Intune。

组织必须完成以下三个步骤，才能要求在移动设备和 Exchange ActiveSync 客户端上使用经批准的客户端应用。

**第 1 步：基于 Android 和 iOS 的现代身份验证客户端的策略，在访问在线 Exchange 和 SharePoint 在线时需要使用经批准的客户端应用程序。**

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
1. 在 **"访问控制** > **授予"** 下，选择 **"授予访问权限****"，"需要已批准的客户端应用**"，然后选择 **"选择**"。
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
1. 在 **"访问控制** > **授予"** 下，选择 **"授予访问权限****"，"需要已批准的客户端应用**"，然后选择 **"选择**"。
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择 **"创建**"以创建并启用策略。

**第 3 步：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略。**

查看文章["如何创建和分配应用保护策略](/intune/apps/app-protection-policies)，了解为 Android 和 iOS 创建应用保护策略的步骤。 

## <a name="next-steps"></a>后续步骤

[什么是条件访问？](overview.md)

[条件访问组件](concept-conditional-access-policies.md)

[常用条件访问策略](concept-conditional-access-policy-common.md)

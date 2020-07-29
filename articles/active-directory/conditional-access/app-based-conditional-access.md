---
title: 使用条件访问的已批准客户端应用 - Azure Active Directory
description: 了解如何使用 Azure Active Directory 中的条件访问要求使用经批准的客户端应用进行云应用访问。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c9cd790edcb186ed2f80d467076512cd558ca40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253386"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>如何：使用条件访问要求使用经批准的客户端应用进行云应用访问

人们会经常将其移动设备用于个人任务和工作任务。 在确保员工工作效率的同时，组织还需防止可能不安全的应用程序中出现数据丢失的情况。 使用条件访问，组织可以让员工只访问批准的（支持新式身份验证的）客户端应用。

本文介绍了两种方案，用于为 Office 365、Exchange Online 和 SharePoint Online 等资源配置条件访问策略。

- [方案 1：Office 365 应用需要已批准的客户端应用](#scenario-1-office-365-apps-require-an-approved-client-app)
- [方案 2：Exchange Online 和 SharePoint Online 需要批准的客户端应用](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

在条件访问中，此功能称为“需要批准的客户端应用”。 有关核准客户端应用程序的列表，请参阅[核准客户端应用程序要求](concept-conditional-access-grant.md#require-approved-client-app)。

> [!NOTE]
> 为了要求 iOS 和 Android 设备使用经批准的客户端应用程序，必须首先在 Azure AD 中注册这些设备。

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>应用场景 1：Office 365 应用需要批准的客户端应用

在此方案中，Contoso 规定：使用移动设备的用户只要使用批准的客户端应用（如 Outlook 移动版、OneDrive 和 Microsoft Teams），就可以访问所有 Office 365 服务。 其所有用户已使用 Azure AD 凭据登录，并获得了分配的许可证，其中包括 Azure AD Premium P1 或 P2 以及 Microsoft Intune。

组织必须完成以下三个步骤才能要求在移动设备上使用批准的客户端应用。

**步骤 1：基于 Android 和 iOS 的新式身份验证客户端的策略，要求在访问 Exchange Online 时使用批准的客户端应用程序。**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下选择“所有用户”，或选择你希望对其应用此策略的具体“用户和组”。    
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“Office 365 (预览版)”。 
1. 在“条件”下，选择“设备平台”。  
   1. 将“配置”设置为“是”。  
   1. 包括 Android 和 iOS。  
1. 在“条件”下，选择“客户端应用(预览版)”。  
   1. 将“配置”设置为“是”。  
   1. 选择“移动应用和桌面客户端”和“新式身份验证客户端”。  
1. 在“访问控制”   >   “授予”下，依次选择“授予访问权限”、“需要批准的客户端应用”、“选择”。   
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建并启用策略。

**步骤 2：为 Exchange Online with ActiveSync (EAS) 配置 Azure AD 条件访问策略**

1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下选择“所有用户”，或选择你希望对其应用此策略的具体“用户和组”。    
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“Office 365 Exchange Online”。 
1. 在“条件”  下，执行以下操作：
   1. 客户端应用(预览版)  ：
      1. 将“配置”设置为“是”。  
      1. 选择“移动应用和桌面客户端”和“Exchange ActiveSync 客户端”。  
1. 在“访问控制”   >   “授予”下，依次选择“授予访问权限”、“需要批准的客户端应用”、“选择”。   
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建并启用策略。

**步骤 3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略。**

查看[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文，了解为 Android 和 iOS 创建应用保护策略的步骤。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>应用场景 2：Exchange Online 和 SharePoint Online 需要批准的客户端应用

在此方案中，Contoso 规定：用户在使用批准的客户端应用（例如 Outlook 移动版）时，只能访问移动设备上的电子邮件和 SharePoint 数据。 其所有用户已使用 Azure AD 凭据登录，并获得了分配的许可证，其中包括 Azure AD Premium P1 或 P2 以及 Microsoft Intune。

组织必须完成以下三个步骤才能要求在移动设备和 Exchange ActiveSync 客户端上使用批准的客户端应用。

**步骤 1：基于 Android 和 iOS 的新式身份验证客户端的策略，要求在访问 Exchange Online 和 SharePoint Online 时使用批准的客户端应用程序。**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下选择“所有用户”，或选择你希望对其应用此策略的具体“用户和组”。    
   1. 选择“完成”  。
1. 在“云应用或操作” > “包括”下，选择“Office 365 Exchange Online”和“Office 365 SharePoint Online”     。
1. 在“条件”下，选择“设备平台”。  
   1. 将“配置”设置为“是”。  
   1. 包括 Android 和 iOS。  
1. 在“条件”下，选择“客户端应用(预览版)”。  
   1. 将“配置”设置为“是”。  
   1. 选择“移动应用和桌面客户端”和“新式身份验证客户端”。  
1. 在“访问控制”   >   “授予”下，依次选择“授予访问权限”、“需要批准的客户端应用”、“选择”。   
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建并启用策略。

**步骤 2：Exchange ActiveSync 客户端的策略，要求使用批准的客户端应用。**

1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下选择“所有用户”，或选择你希望对其应用此策略的具体“用户和组”。    
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“Office 365 Exchange Online”。 
1. 在“条件”  下，执行以下操作：
   1. 客户端应用(预览版)  ：
      1. 将“配置”设置为“是”。  
      1. 选择“移动应用和桌面客户端”和“Exchange ActiveSync 客户端”。  
1. 在“访问控制”   >   “授予”下，依次选择“授予访问权限”、“需要批准的客户端应用”、“选择”。   
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建并启用策略。

**步骤 3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略。**

查看[如何创建和分配应用保护策略](/intune/apps/app-protection-policies)一文，了解为 Android 和 iOS 创建应用保护策略的步骤。 

## <a name="next-steps"></a>后续步骤

[什么是条件访问？](overview.md)

[条件访问组件](concept-conditional-access-policies.md)

[常用条件访问策略](concept-conditional-access-policy-common.md)

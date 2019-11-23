---
title: Approved client apps with Conditional Access - Azure Active Directory
description: Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381125"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How To: Require approved client apps for cloud app access with Conditional Access 

员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 With Azure Active Directory (Azure AD) Conditional Access, you can restrict access to your cloud apps to approved client apps that can protect your corporate data.  

本主题介绍了如何配置要求使用经批准的客户端应用的条件访问策略。

## <a name="overview"></a>概述

With [Azure AD Conditional Access](overview.md), you can fine-tune how authorized users can access your resources. 例如，可以限制为只允许受信任的设备访问云应用程序。

可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据。 Intune 应用程序保护策略不要求使用移动设备管理 (MDM) 解决方案，即无论是否在设备管理解决方案中注册设备，都可以帮助保护公司数据。

Azure Active Directory Conditional Access enables you to limit access to your cloud apps to client apps that support Intune app protection policies. 例如，可以限制为只有 Outlook 应用程序，才能访问 Exchange Online。

In the Conditional Access terminology, these client apps are known as **approved client apps**.  

![条件访问](./media/app-based-conditional-access/05.png)

有关核准客户端应用程序的列表，请参阅[核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)。

You can combine app-based Conditional Access policies with other policies such as [device-based Conditional Access policies](require-managed-devices.md) to provide flexibility in how to protect data for both personal and corporate devices.

## <a name="before-you-begin"></a>开始之前

本主题假定你熟悉以下内容：

- [核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)技术参考。
- The basic concepts of [Conditional Access in Azure Active Directory](overview.md).
- How to [configure a Conditional Access policy](app-based-mfa.md).
- The [migration of Conditional Access policies](best-practices.md#policy-migration).

## <a name="prerequisites"></a>必备组件

To create an app-based Conditional Access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 

## <a name="exchange-online-policy"></a>Exchange Online 策略 

This scenario consists of an app-based Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 使用 iOS 或 Android 上的本地邮件应用程序配置电子邮件，以连接到 Exchange
- 收到一封电子邮件，邮件指示只能通过 Outlook 应用进行访问
- 通过链接下载该应用程序
- 打开 Outlook 应用程序，并使用 Azure AD 凭据登录
- 收到系统提示，要求安装验证器 (iOS) 或公司门户 (Android) 以继续操作
- 安装该应用程序，并可返回 Outlook 应用以继续操作
- 收到系统提示，要求注册设备
- 可访问电子邮件

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform).

### <a name="configuration"></a>配置 

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online。
1. **条件：** 对于“条件”，需配置“设备平台”和“客户端应用”：
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

   ![条件访问](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online。
1. **条件：** 对于“条件”，需要配置“客户端应用(预览版)”。 
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
   1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

      ![条件访问](./media/app-based-conditional-access/05.png)

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 和 SharePoint Online 策略

This scenario consists of a Conditional Access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 尝试使用 SharePoint 应用进行连接并查看其公司站点
- Attempts to sign in with the same credentials as the Outlook app credentials
- 无需重新注册并且可以获取对资源的访问权限

### <a name="configuration"></a>配置

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online 和 Office 365 SharePoint Online。 
1. **条件：** 对于“条件”，需配置“设备平台”和“客户端应用”：
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

   ![条件访问](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online。 联机 
1. **条件：** 对于“条件”，无需配置“客户端应用”：
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
   1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

      ![条件访问](./media/app-based-conditional-access/05.png)

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>用于访问 Exchange Online 和 SharePoint Online 的基于应用程序或相容设备策略

This scenario consists of an app-based or compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>方案演练

此方案假定：
 
- Some users are already enrolled (with or without corporate devices)
- 未使用受保护应用程序向 Azure AD 登记并注册的用户需注册设备，获取资源访问权限
- 已使用受保护应用程序注册的用户无需重新注册设备

### <a name="configuration"></a>配置

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online 和 Office 365 SharePoint Online。 
1. **条件：** 对于“条件”，无需配置“设备平台”和“客户端应用”。 
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中以下内容：
   - “要求将设备标记为合规”
   - “需要批准的客户端应用(预览)”
   - “需要某一已选控件”   
 
      ![条件访问](./media/app-based-conditional-access/11.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online。 
1. **条件：** 对于“条件”，无需配置“客户端应用”. 
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。
 
   ![条件访问](./media/app-based-conditional-access/11.png)

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>用于访问 Exchange Online 和 SharePoint Online 的基于应用程序和相容设备策略

This scenario consists of an app-based and compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：
 
- 使用 iOS 或 Android 上的本地邮件应用程序配置电子邮件，以连接到 Exchange
- 收到一封电子邮件，邮件指示需注册设备才能进行访问
- 下载公司门户并登录公司门户
- 检查邮件，并且要求使用 Outlook 应用
- 下载 Outlook 应用
- 打开 Outlook 应用，并输入注册时使用的凭据
- 可访问电子邮件

只要访问公司数据，任何 Intune 应用保护策略都会被激活，且会提示用户重启应用程序，使用其他 PIN 码等（前提是为应用程序和平台配置了策略）。

### <a name="configuration"></a>配置

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online 和 Office 365 SharePoint Online。 
1. **条件：** 对于“条件”，无需配置“设备平台”和“客户端应用”。 
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中以下内容：
   - “要求将设备标记为合规”
   - “需要批准的客户端应用(预览)”
   - ”需要所有已选控件“   
 
      ![条件访问](./media/app-based-conditional-access/13.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **云应用：** 对于云应用，需选择 Office 365 Exchange Online。 
1. **条件：** 对于“条件”，需要配置“客户端应用(预览版)”。 
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。

   ![条件访问](./media/app-based-conditional-access/92.png)

1. 对于“访问控制”，需选中以下内容：
   - “要求将设备标记为合规”
   - “需要批准的客户端应用(预览)”
   - ”需要所有已选控件“   

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="next-steps"></a>后续步骤

若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。

如果已准备好针对环境配置条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](best-practices.md)。 

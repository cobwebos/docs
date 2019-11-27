---
title: 具有条件访问的应用保护策略-Azure Active Directory
description: 了解如何通过 Azure Active Directory 中的条件访问要求使用应用保护策略进行云应用访问。
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
ms.openlocfilehash: b3cbb6afb96ccea32aa78d1f587377e5d67e1a5b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381021"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>通过条件访问（预览版）要求使用应用保护策略进行云应用访问

员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 借助 Azure Active Directory (Azure AD) 条件访问，可以通过限制对云应用的访问来保护企业数据。 先将客户端应用与应用保护策略配合使用。

本文介绍如何配置条件访问策略，以便在授予数据访问权限之前要求使用应用保护策略。

## <a name="overview"></a>概述

借助 [Azure AD 条件访问](overview.md)，可以微调授权用户访问资源的方式。 例如，可以限制为只允许受信任的设备访问云应用程序。

可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据。 Intune 应用保护策略不要求使用移动设备管理 (MDM) 解决方案。 无论是否在设备管理解决方案中注册了设备，都可以保护公司的数据。

Azure Active Directory 条件访问将云应用的访问权限限制为 Intune 向 Azure AD 报告的、收到了应用保护策略的客户端应用程序。 例如，可以限制为只有设置了 Intune 应用保护策略的 Outlook 应用才能访问 Exchange Outlook。

在条件访问术语中，这些客户端应用称为使用应用保护策略保护的策略。  

![条件性访问](./media/app-protection-based-conditional-access/05.png)

有关受策略保护的客户端应用列表，请参阅[应用保护策略要求](technical-reference.md#approved-client-app-requirement)。

可将基于应用保护的条件访问策略与其他策略（例如[基于设备的条件访问策略](require-managed-devices.md)）相结合。 这样，就可以灵活地保护个人和企业设备的数据。

> [!NOTE]
> 由于邀请的组织无法查看 B2B 用户的本组织，因此无法将条件访问应用保护策略应用于 B2B 用户。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>基于应用保护的条件访问的优势

与适用于 iOS 和 Android 的 Intune 报告托管设备合规性类似，如果应用了应用保护策略，则 Intune 现在会向 Azure AD 报告。 条件访问可以使用此策略作为访问检查。 新的条件访问策略“应用保护策略”提高了安全性。 它可以防范管理错误，例如：

- 用户没有 Intune 许可证。
- 用户无法接收 Intune 应用保护策略。
- 未将 Intune 应用保护策略应用配置为接收策略。

## <a name="before-you-begin"></a>开始之前

本文假定你熟悉以下内容：

- [应用保护策略要求](technical-reference.md#app-protection-policy-requirement)技术参考。
- [核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)技术参考。
- [Azure Active Directory 中的条件访问](overview.md)的基本概念。
- 如何[配置条件访问策略](app-based-mfa.md)。

## <a name="prerequisites"></a>先决条件

若要创建基于应用保护的条件访问策略，必须：

- 具有企业移动性 + 安全性或 Azure Active Directory Premium 订阅 + Intune。
- 确保用户已获得企业移动性 + 安全性或 Azure AD + Intune 的许可证。
- 确保在 Intune 中将客户端应用配置为接收应用保护策略。
- 确保在 Intune 中将用户配置为接收 Intune 应用保护策略。

## <a name="app-protection-based-policy-for-exchange-online"></a>适用于 Exchange Online 的基于应用保护的策略

此方案包含的基于应用保护的条件访问策略旨在访问 Exchange Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 使用 iOS 或 Android 上的本地邮件应用程序配置电子邮件，以连接到 Exchange。
- 收到一封电子邮件，其中指出只能使用 Outlook 应用进行访问。
- 通过链接下载该应用程序。
- 打开 Outlook 应用程序，并使用 Azure AD 凭据登录。
- 收到系统提示，要求安装 **Microsoft Authenticator 应用**或 **Intune 公司门户**以继续操作。
- 安装该应用程序，并返回 Outlook 应用以继续操作。
- 收到系统提示，要求注册设备。
- 可以接收 Intune 应用保护策略。
- 可以访问电子邮件。

任何 Intune 应用保护策略必须位于应用程序中才能访问企业数据。 这些策略可能会提示用户重启应用程序或使用其他 PIN。 如果策略是针对应用程序和平台配置的，则会出现这种情况。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/01.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。

   ![条件性访问](./media/app-protection-based-conditional-access/07.png)

1. 在“条件”中，配置“设备平台”和“客户端应用(预览版)”：
   1. 在“设备平台”中，选择“Android”和“iOS”。

      ![条件性访问](./media/app-protection-based-conditional-access/03.png)

   1. 在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“新式身份验证客户端”。

      ![条件性访问](./media/app-protection-based-conditional-access/91.png)

1. 在“访问控制”下，选择“需要应用保护策略(预览版)”。

   ![条件性访问](./media/app-protection-based-conditional-access/05.png)

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略（EAS）**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。

   ![条件性访问](./media/app-protection-based-conditional-access/07.png)

1. 在“条件”中，配置“客户端应用(预览版)”。 

   1. 在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“Exchange ActiveSync 客户端”。

      ![条件性访问](./media/app-protection-based-conditional-access/92.png)

   1. 在“访问控制”下，选择“需要应用保护策略(预览版)”。

      ![条件性访问](./media/app-protection-based-conditional-access/05.png)

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>适用于 Exchange Online 的基于应用保护的策略或合规设备策略

此方案包含的基于应用保护或合规设备条件访问策略旨在访问 Exchange Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定：
 
- 用户已注册（无论是否使用企业设备）。
- 未使用受保护应用程序向 Azure AD 登记并注册的用户需注册设备才能访问资源。
- 已使用受保护应用程序注册的用户无需重新注册设备。
- 未登记的用户可以接收 Intune 应用保护策略。
- 未登记的用户可以使用 Outlook 和 Intune 应用保护策略访问电子邮件。
- 如果设备已登记，则用户可以使用 Outlook 访问电子邮件。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/62.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。 

   ![条件性访问](./media/app-protection-based-conditional-access/07.png)

1. 在“条件”中，配置“设备平台”和“客户端应用(预览版)”。 
 
   1. 在“设备平台”中，选择“Android”和“iOS”。

      ![条件性访问](./media/app-protection-based-conditional-access/03.png)

   1. 在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“新式身份验证客户端”。

      ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 在“访问控制”下，选择以下选项：
   - “要求将设备标记为合规”
   - “需要应用保护策略(预览版)”
   - “需要某一已选控件”   
 
      ![条件性访问](./media/app-protection-based-conditional-access/11.png)

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。 

   ![条件性访问](./media/app-protection-based-conditional-access/07.png)

1. 在“条件”中，配置“客户端应用(预览版)”。 

   在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“Exchange ActiveSync 客户端”。

   ![条件性访问](./media/app-protection-based-conditional-access/92.png)

1. 在“访问控制”下，选择以下选项：
   - “要求将设备标记为合规”
   - “需要应用保护策略(预览版)”
   - “需要某一已选控件”

      ![条件性访问](./media/app-protection-based-conditional-access/11.png)

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>适用于 Exchange Online 的基于应用保护的策略与合规设备策略

此方案包含的基于应用保护与合规设备条件访问策略旨在访问 Exchange Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：
 
- 使用 iOS 或 Android 上的本地邮件应用程序配置电子邮件，以连接到 Exchange。
- 收到一封电子邮件，其中指出需要注册设备才能进行访问。
- 下载 Intune 公司门户并登录到该门户。
- 检查邮件，其中要求使用 Outlook 应用。
- 下载 Outlook 应用。
- 打开 Outlook 应用，并输入注册时使用的凭据。
- 可以接收 Intune 应用保护策略。
- 可以使用 Outlook 和 Intune 应用保护策略访问电子邮件。

在授予公司数据访问权限之前激活任何 Intune 应用保护策略。 这些策略可能会提示用户重启应用程序或使用其他 PIN。 如果策略是针对应用程序和平台配置的，则会出现这种情况。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/01.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。 

   ![条件性访问](./media/app-protection-based-conditional-access/07.png)

1. 在“条件”中，配置“设备平台”和“客户端应用(预览版)”。 
   1. 在“设备平台”中，选择“Android”和“iOS”。

      ![条件性访问](./media/app-protection-based-conditional-access/03.png)

   1. 在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“新式身份验证客户端”。

      ![条件性访问](./media/app-protection-based-conditional-access/91.png)

1. 在“访问控制”下，选择以下选项：
   - “要求将设备标记为合规”
   - “需要应用保护策略(预览版)”
   - ”需要所有已选控件“   
 
      ![条件性访问](./media/app-protection-based-conditional-access/13.png)

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。 

   ![条件性访问](./media/app-protection-based-conditional-access/07.png)

1. 在“条件”中，配置“客户端应用(预览版)”。 

   在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“Exchange ActiveSync 客户端”。

   ![条件性访问](./media/app-protection-based-conditional-access/92.png)

1. 在“访问控制”下，选择以下选项：
   - “要求将设备标记为合规”
   - “需要应用保护策略(预览版)”
   - ”需要所有已选控件“   
 
      ![条件性访问](./media/app-protection-based-conditional-access/13.png)

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>适用于 Exchange Online 和 SharePoint Online 的基于应用保护的策略或合规设备策略

此方案包含的基于应用保护或已批准应用策略旨在访问 Exchange Online 和 SharePoint Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 配置客户端应用程序，这些应用程序包含在支持应用保护策略要求或已批准应用要求的应用列表中。  
- 使用符合应用保护策略要求并可以接收 Intune 应用保护策略的客户端应用程序。
- 使用符合已批准应用策略要求并支持 Intune 应用保护策略的客户端应用程序。
- 打开应用程序以访问电子邮件或文档。
- 打开 Outlook 应用程序，并使用 Azure AD 凭据登录。
- 收到系统提示，要求安装适用于 iOS 的 Microsoft Authenticator 或适用于 Android 的 Intune 公司门户（如果尚未安装）。
- 安装该应用程序，并可返回 Outlook 应用以继续操作。
- 收到系统提示，要求注册设备。
- 可以接收 Intune 应用保护策略。
- 可以使用 Outlook 和 Intune 应用保护策略访问电子邮件。
- 可以使用未列在应用保护策略要求中，但已列在已批准应用要求中的应用访问站点和文档。

任何 Intune 应用保护策略需要在授予公司数据访问权限之前使用。 这些策略可能会提示用户重启应用程序或使用其他 PIN。 如果策略是针对应用程序和平台配置的，则会出现这种情况。

**备注**

- 若要同时支持基于应用保护和基于应用的条件访问策略，可以使用此方案。
- 在此 *OR* 策略中，将先评估具有应用保护策略要求的应用的访问，然后再评估具有已批准客户端应用要求的应用的访问。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，请配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/62.png)

1. 输入条件访问策略的名称。
1. 在“用户和组”中的“分配”下，为每个条件访问策略至少选择一个用户或组。
1. 在“云应用”中，选择“Office 365 Exchange Online”。 

   ![条件性访问](./media/app-protection-based-conditional-access/02.png)

1. 在“条件”中，配置“设备平台”和“客户端应用(预览版)”。 
   1. 在“设备平台”中，选择“Android”和“iOS”。

      ![条件性访问](./media/app-protection-based-conditional-access/03.png)

   1. 在“客户端应用(预览版)”中，选择“移动应用和桌面客户端”和“新式身份验证客户端”。

      ![条件性访问](./media/app-protection-based-conditional-access/91.png)

1. 在“访问控制”下，选择以下选项：
   - “需要已批准的客户端应用”
   - “需要应用保护策略(预览版)”
   - “需要某一已选控件”
 
      ![条件性访问](./media/app-protection-based-conditional-access/12.png)

**步骤2：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。
- 如果你已准备好针对环境配置条件访问策略，请参阅 [Azure Active Directory 中的条件访问的最佳做法](best-practices.md)。

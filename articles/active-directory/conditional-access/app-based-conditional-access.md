---
title: 如何使用 Azure Active Directory 中的条件访问来要求批准的云应用访问客户端应用 |Microsoft Docs
description: 了解如何在 Azure Active Directory 中使用条件访问来要求批准的客户端应用进行云应用访问。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45eeef7d96f194e224e5b44421e73eb5ee5d9c0d
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515146"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>如何：需要经过批准的客户端应用才能使用条件访问访问 cloud app 

员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 使用 Azure Active Directory (Azure AD) 条件性访问, 你可以将云应用的访问权限限制为可保护你的公司数据的批准的客户端应用。  

本主题介绍了如何配置要求使用经批准的客户端应用的条件访问策略。

## <a name="overview"></a>概述

使用[Azure AD 条件性访问](overview.md), 你可以微调已授权用户访问资源的方式。 例如，可以限制为只允许受信任的设备访问云应用程序。

可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据。 Intune 应用程序保护策略不要求使用移动设备管理 (MDM) 解决方案，即无论是否在设备管理解决方案中注册设备，都可以帮助保护公司数据。

Azure Active Directory 条件访问允许你将云应用的访问权限限制为支持 Intune 应用保护策略的客户端应用。 例如，可以限制为只有 Outlook 应用程序，才能访问 Exchange Online。

在条件访问术语中, 这些客户端应用被称为**批准的客户端应用**。  

![条件访问](./media/app-based-conditional-access/05.png)

有关核准客户端应用程序的列表，请参阅[核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)。

你可以结合使用基于应用的条件性访问策略与其他策略 (如[基于设备的条件访问策略](require-managed-devices.md)) 来灵活地保护个人和公司设备的数据。

## <a name="before-you-begin"></a>开始之前

本主题假定你熟悉以下内容：

- [核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)技术参考。
- [Azure Active Directory 中条件性访问](overview.md)的基本概念。
- 如何[配置条件性访问策略](app-based-mfa.md)。
- [条件访问策略的迁移](best-practices.md#policy-migration)。

## <a name="prerequisites"></a>先决条件

若要创建基于应用的条件性访问策略, 必须具有企业移动性 + 安全性或 Azure Active Directory premium 订阅, 并且用户必须获得 EMS 或 Azure AD 许可。 

## <a name="exchange-online-policy"></a>Exchange Online 策略 

此方案包含用于访问 Exchange Online 的基于应用的条件性访问策略。

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

在访问公司数据时将激活任何 Intune 应用保护策略, 并可能提示用户重新启动应用程序、使用额外的 PIN 等。 (如果是为应用程序和平台配置的)。

### <a name="configuration"></a>配置 

**步骤 1-为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。
1. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”：
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

   ![条件访问](./media/app-based-conditional-access/05.png)

**步骤 2-使用 Active Sync (EAS) 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。
1. **条件：** 对于“条件”，需要配置“客户端应用(预览版)”。 
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
   1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

      ![条件访问](./media/app-based-conditional-access/05.png)

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 和 SharePoint Online 策略

此方案包含使用移动应用管理策略的条件性访问, 可通过已批准的应用访问 Exchange Online 和 SharePoint Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 尝试使用 SharePoint 应用进行连接并查看其公司站点
- 尝试以 Outlook 应用凭据的相同凭据登录
- 无需重新注册并且可以获取对资源的访问权限

### <a name="configuration"></a>配置

**步骤 1-为 Exchange Online 和 SharePoint Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”和“Office 365 SharePoint Online”。 
1. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”：
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

   ![条件访问](./media/app-based-conditional-access/05.png)

**步骤 2-使用 Active Sync (EAS) 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 Online 
1. **条件：** 对于“条件”，需要配置“客户端应用”：
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
   1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。

      ![条件访问](./media/app-based-conditional-access/05.png)

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>用于访问 Exchange Online 和 SharePoint Online 的基于应用程序或相容设备策略

此方案包含用于访问 Exchange Online 的基于应用或合规的设备条件访问策略。

### <a name="scenario-playbook"></a>方案演练

此方案假定：
 
- 某些用户已注册 (有或没有企业设备)
- 未使用受保护应用程序向 Azure AD 登记并注册的用户需注册设备，获取资源访问权限
- 已使用受保护应用程序注册的用户无需重新注册设备

### <a name="configuration"></a>配置

**步骤 1-为 Exchange Online 和 SharePoint Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”和“Office 365 SharePoint Online”。 
1. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”。 
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中以下内容：
   - “要求将设备标记为合规”
   - “需要批准的客户端应用(预览)”
   - “需要某一已选控件”   
 
      ![条件访问](./media/app-based-conditional-access/11.png)

**步骤 2-使用 Active Sync (EAS) 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 
1. **条件：** 对于“条件”，需要配置“客户端应用”。 
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。
1. 对于“访问控制”，需选中“需要批准的客户端应用(预览)”。
 
   ![条件访问](./media/app-based-conditional-access/11.png)

**步骤 3 - 为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>用于访问 Exchange Online 和 SharePoint Online 的基于应用程序和相容设备策略

此方案包含用于访问 Exchange Online 的基于应用和相容设备条件访问策略。

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

**步骤 1-为 Exchange Online 和 SharePoint Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”和“Office 365 SharePoint Online”。 
1. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”。 
   1. 对于“设备平台”，请选择 Android 和 iOS。
   1. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。
1. 对于“访问控制”，需选中以下内容：
   - “要求将设备标记为合规”
   - “需要批准的客户端应用(预览)”
   - ”需要所有已选控件“   
 
      ![条件访问](./media/app-based-conditional-access/13.png)

**步骤 2-使用 Active Sync (EAS) 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略, 需要配置以下组件:

1. 条件访问策略的**名称**。
1. **用户和组**：每个条件访问策略都必须至少选择一个用户或组。
1. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 
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

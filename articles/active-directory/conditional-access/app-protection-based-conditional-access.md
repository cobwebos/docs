---
title: 具有条件访问的应用保护策略-Azure Active Directory
description: 了解如何在 Azure Active Directory 中使用条件性访问来要求应用保护策略。
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
ms.openlocfilehash: bbbe882acda532a54333ca8013693121d5b677b7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964083"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>需要使用条件性访问（预览版）进行 cloud 应用访问的应用保护策略

员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 使用 Azure Active Directory （Azure AD）条件性访问，你可以通过限制对你的云应用的访问来保护公司数据。 先将客户端应用与应用保护策略一起使用。

本文介绍如何配置条件访问策略，这些策略可能需要在向数据授予访问权限之前使用应用保护策略。

## <a name="overview"></a>概述

使用[Azure AD 条件性访问](overview.md)，你可以微调已授权用户访问资源的方式。 例如，可以限制为只允许受信任的设备访问云应用程序。

可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据。 Intune 应用保护策略不需要移动设备管理（MDM）解决方案。 你可以使用或不在设备管理解决方案中注册设备来保护公司的数据。

Azure Active Directory 条件访问限制了对云应用的访问权限，该客户端应用程序在 Intune 报告为接收应用保护策略 Azure AD。 例如，你可以将对 Exchange Online 的访问限制为具有 Intune 应用保护策略的 Outlook 应用。

在条件访问术语中，这些客户端应用被认为是使用*应用保护策略*保护的策略。  

![条件访问](./media/app-protection-based-conditional-access/05.png)

有关受策略保护的客户端应用的列表，请参阅[应用保护策略要求](technical-reference.md#approved-client-app-requirement)。

可以将基于应用保护的条件性访问策略与其他策略（如[基于设备的条件访问策略](require-managed-devices.md)）结合使用。 这样，你就可以灵活地保护个人和公司设备的数据。

> [!NOTE]
> 由于邀请的组织无法查看 B2B 用户的本组织，因此无法将条件访问应用保护策略应用于 B2B 用户。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>基于应用保护的条件性访问要求的优点

与用于管理设备的 Intune for iOS 和 Android 报告的符合性类似，如果应用了应用保护策略，则 Intune 现在将报告为 Azure AD。 条件性访问可以使用此策略作为访问检查。 这一新的条件性访问策略是应用保护策略，增加了安全性。 它可以防止出现管理错误，如：

- 没有 Intune 许可证的用户。
- 不能接收 Intune 应用保护策略的用户。
- 未配置为接收策略的 Intune 应用保护策略应用。

## <a name="before-you-begin"></a>开始之前

本文假定你熟悉以下内容：

- [应用保护策略要求](technical-reference.md#app-protection-policy-requirement)技术参考。
- [核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)技术参考。
- [Azure Active Directory 中条件性访问](overview.md)的基本概念。
- 如何[配置条件性访问策略](app-based-mfa.md)。

## <a name="prerequisites"></a>必备组件

若要创建基于应用保护的条件性访问策略，必须执行以下操作：

- 拥有企业移动性 + 安全性或 Azure Active Directory premium 订阅 + Intune。
- 请确保用户已获得企业移动性 + 安全性或 Azure AD + Intune 许可。
- 请确保在 Intune 中配置客户端应用以接收应用保护策略。
- 请确保在 Intune 中配置用户以接收 Intune 应用保护策略。

## <a name="app-protection-based-policy-for-exchange-online"></a>适用于 Exchange Online 的基于应用保护的策略

此方案包含用于访问 Exchange Online 的基于应用保护的条件性访问策略。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 使用 iOS 或 Android 上的本机邮件应用程序连接到 Exchange 来配置电子邮件。
- 收到一封电子邮件，指示访问仅可通过使用 Outlook 应用。
- 下载包含链接的应用程序。
- 打开 Outlook 应用程序，并 Azure AD 凭据登录。
- 系统将提示安装**Microsoft Authenticator 应用**或**Intune 公司门户**以继续。
- 安装应用程序并返回到 Outlook 应用程序以继续。
- 系统将提示注册设备。
- 可以接收 Intune 应用保护策略。
- 可访问电子邮件。

任何 Intune 应用保护策略都必须位于应用程序上才能访问公司数据。 这些策略可能会提示用户重新启动应用程序或使用额外的 PIN。 如果为应用程序和平台配置了策略，则会出现这种情况。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/01.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择 " **Office 365 Exchange Online**"。

   ![条件访问](./media/app-protection-based-conditional-access/07.png)

1. 在 "**条件**" 中，配置**设备平台**和**客户端应用（预览）** ：
   1. 在**设备平台**中，选择**Android**和**iOS**。

      ![条件访问](./media/app-protection-based-conditional-access/03.png)

   1. 在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**新式身份验证客户端**"。

      ![条件访问](./media/app-protection-based-conditional-access/91.png)

1. 在 "**访问控制**" 下，选择 "**需要应用保护策略（预览版）** "。

   ![条件访问](./media/app-protection-based-conditional-access/05.png)

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略（EAS）**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/06.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择 " **Office 365 Exchange Online**"。

   ![条件访问](./media/app-protection-based-conditional-access/07.png)

1. 在 "**条件**" 中，配置**客户端应用（预览）** 。 

   1. 在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**Exchange ActiveSync 客户端**"。

      ![条件访问](./media/app-protection-based-conditional-access/92.png)

   1. 在 "**访问控制**" 下，选择 "**需要应用保护策略（预览版）** "。

      ![条件访问](./media/app-protection-based-conditional-access/05.png)

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[通过 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>适用于 Exchange Online 的基于应用保护或合规设备策略

此方案包含用于访问 Exchange Online 的基于应用保护或合规的设备条件访问策略。

### <a name="scenario-playbook"></a>方案演练

此方案假定：
 
- 用户已注册，有或没有企业设备。
- 未使用受保护应用程序注册并注册到 Azure AD 的用户需要注册设备才能访问资源。
- 使用受保护应用的应用程序的已注册用户无需重新注册设备。
- 如果未注册，则用户可以接收 Intune 应用保护策略。
- 如果未注册，则用户可以使用 Outlook 和 Intune 应用保护策略访问电子邮件。
- 如果注册了设备，则用户可以使用 Outlook 访问电子邮件。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/62.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择 " **Office 365 Exchange Online**"。 

   ![条件访问](./media/app-protection-based-conditional-access/07.png)

1. 在 "**条件**" 中，配置**设备平台**和**客户端应用（预览）** 。 
 
   1. 在**设备平台**中，选择**Android**和**iOS**。

      ![条件访问](./media/app-protection-based-conditional-access/03.png)

   1. 在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**新式身份验证客户端**"。

      ![条件访问](./media/app-protection-based-conditional-access/91.png)

5. 在 "**访问控制**" 下，选择以下选项：
   - “要求将设备标记为合规”
   - **需要应用保护策略（预览）**
   - “需要某一已选控件”   
 
      ![条件访问](./media/app-protection-based-conditional-access/11.png)

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/06.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择 " **Office 365 Exchange Online**"。 

   ![条件访问](./media/app-protection-based-conditional-access/07.png)

1. 在 "**条件**" 中，配置**客户端应用（预览）** 。 

   在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**Exchange ActiveSync 客户端**"。

   ![条件访问](./media/app-protection-based-conditional-access/92.png)

1. 在 "**访问控制**" 下，选择以下选项：
   - “要求将设备标记为合规”
   - **需要应用保护策略（预览）**
   - “需要某一已选控件”

      ![条件访问](./media/app-protection-based-conditional-access/11.png)

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[通过 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>适用于 Exchange Online 的基于应用保护和合规设备策略

此方案包含用于访问 Exchange Online 的基于应用保护且合规的设备条件访问策略。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：
 
- 使用 iOS 或 Android 上的本机邮件应用程序连接到 Exchange 来配置电子邮件。
- 收到一封电子邮件，指示访问需要注册其设备。
- 下载 Intune 公司门户并登录到门户。
- 检查邮件并要求使用 Outlook 应用。
- 下载 Outlook 应用。
- 打开 Outlook 应用并输入注册中使用的凭据。
- 可以接收 Intune 应用保护策略。
- 可以使用 Outlook 和 Intune 应用保护策略访问电子邮件。

在授予对公司数据的访问权限之前，将激活任何 Intune 应用保护策略。 这些策略可能会提示用户重新启动应用程序或使用额外的 PIN。 如果为应用程序和平台配置了策略，则会出现这种情况。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/01.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择 " **Office 365 Exchange Online**"。 

   ![条件访问](./media/app-protection-based-conditional-access/07.png)

1. 在 "**条件**" 中，配置**设备平台**和**客户端应用（预览）** 。 
   1. 在**设备平台**中，选择**Android**和**iOS**。

      ![条件访问](./media/app-protection-based-conditional-access/03.png)

   1. 在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**新式身份验证客户端**"。

      ![条件访问](./media/app-protection-based-conditional-access/91.png)

1. 在 "**访问控制**" 下，选择以下选项：
   - “要求将设备标记为合规”
   - **需要应用保护策略（预览）**
   - ”需要所有已选控件“   
 
      ![条件访问](./media/app-protection-based-conditional-access/13.png)

**步骤2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/06.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择 " **Office 365 Exchange Online**"。 

   ![条件访问](./media/app-protection-based-conditional-access/07.png)

1. 在 "**条件**" 中，配置**客户端应用（预览）** 。 

   在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**Exchange ActiveSync 客户端**"。

   ![条件访问](./media/app-protection-based-conditional-access/92.png)

1. 在 "**访问控制**" 下，选择以下选项：
   - “要求将设备标记为合规”
   - **需要应用保护策略（预览）**
   - ”需要所有已选控件“   
 
      ![条件访问](./media/app-protection-based-conditional-access/13.png)

**步骤3：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[通过 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>适用于 Exchange Online 和 SharePoint Online 的基于应用保护或基于应用的策略

此方案包含用于访问 Exchange Online 和 SharePoint Online 的基于应用保护或已批准的应用策略。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 配置客户端应用程序，这些应用程序位于支持应用保护策略要求或已批准应用要求的应用列表中。  
- 使用满足应用保护策略要求的客户端应用程序，并可以接收 Intune 应用保护策略。
- 使用满足支持 Intune 应用保护策略的已批准应用策略要求的客户端应用程序。
- 打开应用程序以访问电子邮件或文档。
- 打开 Outlook 应用程序，并 Azure AD 凭据登录。
- 如果尚未安装，则系统将提示安装适用于 iOS 的 Microsoft Authenticator 或适用于 Android 的 Intune 公司门户。
- 安装应用程序并返回到 Outlook 应用程序以继续。
- 系统将提示注册设备。
- 可以接收 Intune 应用保护策略。
- 可以使用 Outlook 和 Intune 应用保护策略访问电子邮件。
- 可以通过应用保护策略要求以外的应用访问站点和文档，但在已批准的应用要求中列出。

在授予对公司数据的访问权限之前，需要提供任何 Intune 应用保护策略。 这些策略可能会提示用户重新启动应用程序或使用额外的 PIN。 如果为应用程序和平台配置了策略，则会出现这种情况。

**备注**

- 如果要支持基于应用保护和基于应用的条件访问策略，则可以使用此方案。
- 在此*或*策略中，具有应用保护策略要求的应用首先评估为在批准的客户端应用要求之前访问。

### <a name="configuration"></a>配置

**步骤1：为 Exchange Online 和 SharePoint Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件性访问策略，请配置下列组件：

![条件访问](./media/app-protection-based-conditional-access/62.png)

1. 输入条件访问策略的名称。
1. 在 "**分配**" 下的 "**用户和组**" 中，为每个条件访问策略至少选择一个用户或组。
1. 在 "**云应用**" 中，选择**Office 365 Exchange online**和**office 365 SharePoint online**。 

   ![条件访问](./media/app-protection-based-conditional-access/02.png)

1. 在 "**条件**" 中，配置**设备平台**和**客户端应用（预览）** 。 
   1. 在**设备平台**中，选择**Android**和**iOS**。

      ![条件访问](./media/app-protection-based-conditional-access/03.png)

   1. 在**客户端应用（预览版）** 中，选择 "**移动应用和桌面客户端**和**新式身份验证客户端**"。

      ![条件访问](./media/app-protection-based-conditional-access/91.png)

1. 在 "**访问控制**" 下，选择以下选项：
   - **需要批准的客户端应用**
   - **需要应用保护策略（预览）**
   - “需要某一已选控件”
 
      ![条件访问](./media/app-protection-based-conditional-access/12.png)

**步骤2：为 iOS 和 Android 客户端应用程序配置 Intune 应用保护策略**

![条件访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[通过 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。
- 如果已准备好为环境配置条件访问策略，请参阅[Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。

---
title: 使用 Azure Active Directory 中条件性访问的云应用访问需要应用保护策略 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 中条件性访问的云应用访问需要应用保护策略。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496924"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>使用条件性访问 （预览版） 的云应用访问需要应用保护策略

员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 使用 Azure Active Directory (Azure AD) 条件性访问，可以通过限制对云应用的访问来保护公司数据。 首先，客户端应用程序使用应用保护策略。

本文介绍如何配置数据授予访问权限之前，可能需要应用保护策略的条件性访问策略。

## <a name="overview"></a>概述

借助 [Azure AD 条件访问](overview.md)，可以微调授权用户访问资源的方式。 例如，可以限制为只允许受信任的设备访问云应用程序。

可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据。 Intune 应用保护策略不需要移动设备管理 (MDM) 解决方案。 你可以保护公司数据，无论在设备管理解决方案中注册设备。

Azure Active Directory 条件性访问限制对 Intune 已报告到 Azure AD 为接收应用保护策略的客户端应用程序云应用程序的访问。 例如，可以限制对 Exchange Online 访问到具有 Intune 应用保护策略 Outlook 应用。

在条件性访问术语中，已知这些客户端应用程序可与受保护的策略*应用保护策略*。  

![条件性访问](./media/app-protection-based-conditional-access/05.png)

有关受策略保护的客户端应用程序的列表，请参阅[应用保护策略要求](technical-reference.md#approved-client-app-requirement)。

你可以组合保护基于应用的条件性访问策略与其他策略，如[基于设备的条件性访问策略](require-managed-devices.md)。 这样一来，您可以更灵活的方式来保护个人和公司设备的数据。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>应用程序保护基于条件性访问要求的优点

类似于报告由 Intune 适用于 iOS 和 Android 托管设备，Intune 现在应用报表复制到 Azure AD，如果应用保护策略的符合性。 条件性访问可以使用此策略作为访问检查。 此新的条件性访问策略，应用保护策略，可提高安全性。 它还可以防止管理员错误，如：

- 不具有 Intune 许可证的用户。
- 不能接收 Intune 应用保护策略的用户。
- Intune 应用保护策略的应用未配置为接收策略。


## <a name="before-you-begin"></a>开始之前

本文假定你熟悉以下内容：

- [应用保护策略要求](technical-reference.md#app-protection-policy-requirement)技术参考。
- [核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)技术参考。
- [Azure Active Directory 中的条件访问](overview.md)的基本概念。
- 如何[配置条件访问策略](app-based-mfa.md)。


## <a name="prerequisites"></a>必备组件

若要创建应用保护基于条件性访问策略，必须：

- 拥有企业移动性 + 安全性或 Azure Active Directory premium 订阅 + Intune。
- 请确保用户获得许可用于企业移动性 + 安全性或 Azure AD + Intune。
- 请确保在 Intune 接收应用保护策略中配置客户端应用程序。
- 请确保用户配置 Intune 以接收 Intune 应用保护策略中。

## <a name="app-protection-based-policy-for-exchange-online"></a>为 Exchange Online 应用基于保护策略

此方案包含的应用保护基于条件性访问策略旨在访问 Exchange Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 通过使用 iOS 或 Android 上的本机邮件应用程序连接到 Exchange 中配置电子邮件。
- 会收到电子邮件，以指示访问可用只能通过使用 Outlook 应用。
- 下载与链接的应用程序。
- 打开 Outlook 应用程序并使用 Azure AD 凭据登录。
- 系统会提示安装是用于 iOS 的 Microsoft Authenticator 或 Intune 公司门户，用于 Android 以继续。
- 安装应用程序，并返回到 Outlook 应用以继续操作。
- 系统会提示注册的设备。
- 可以接收 Intune 应用保护策略。
- 可以访问电子邮件。

任何 Intune 应用保护策略必须位于应用程序访问公司数据。 策略可能会提示用户重新启动该应用程序或使用其他的 PIN。 如果为应用程序和平台配置策略，这是这种情况。

### <a name="configuration"></a>配置

**步骤 1：为 Exchange Online 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/01.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。

3. 在中**云应用**，选择**Office 365 Exchange Online**。

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. 在中**条件**，配置**设备平台**并**客户端应用 （预览版）**:

    a. 在中**设备平台**，选择**Android**并**iOS**。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**新式身份验证客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 下**的访问控制**，选择**需要应用保护策略 （预览版）**。

    ![条件性访问](./media/app-protection-based-conditional-access/05.png)
 

**步骤 2：为 Exchange Online 使用 ActiveSync (EAS) 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。


3. 在中**云应用**，选择**Office 365 Exchange Online**。

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. 在中**条件**，配置**客户端应用 （预览版）**。 

    a. 在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**Exchange ActiveSync 客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/92.png)

    b. 下**的访问控制**，选择**需要应用保护策略 （预览版）**。

    ![条件性访问](./media/app-protection-based-conditional-access/05.png)


**步骤 3：配置适用于 iOS 和 Android 客户端应用程序的 Intune 应用保护策略**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>为 Exchange Online 的应用保护基于或相容设备策略

此方案包括 Exchange Online 的访问权限的应用保护基于或相容设备条件访问策略。


### <a name="scenario-playbook"></a>方案演练

此方案假定：
 
- 已注册用户，无论企业设备。
- 不是注册和使用应用程序与 Azure AD 中注册的用户受保护的应用程序需要注册设备以访问资源。
- 使用受保护应用程序的已注册的用户无需重新注册设备。
- 如果未注册，则用户可以接收 Intune 应用保护策略。
- 如果未注册，则用户可以访问电子邮件与 Outlook 和 Intune 应用保护策略。
- 如果注册了设备，用户可以访问包含 Outlook 电子邮件。


### <a name="configuration"></a>配置

**步骤 1：为 Exchange Online 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/62.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。

3. 在中**云应用**，选择**Office 365 Exchange Online**。 

     ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. 在中**条件**，配置**设备平台**并**客户端应用 （预览版）**。 
 
    a. 在中**设备平台**，选择**Android**并**iOS**。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**新式身份验证客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 下**的访问控制**，选择以下选项：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **需要某一已选控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/11.png)



**步骤 2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。

3. 在中**云应用**，选择**Office 365 Exchange Online**。 

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. 在中**条件**，配置**客户端应用 （预览版）**。 

    在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**Exchange ActiveSync 客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/92.png)

5. 下**的访问控制**，选择以下选项：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **需要某一已选控件**

     ![条件性访问](./media/app-protection-based-conditional-access/11.png)



**步骤 3：配置适用于 iOS 和 Android 客户端应用程序的 Intune 应用保护策略**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>为 Exchange Online 应用基于保护和相容设备策略

此方案包含的基于应用的保护和相容设备条件访问策略旨在访问 Exchange Online。


### <a name="scenario-playbook"></a>方案演练

此方案假定用户：
 
-   通过使用 iOS 或 Android 上的本机邮件应用程序连接到 Exchange 中配置电子邮件。
-   收到指示访问需要注册其设备的电子邮件。
-   下载 Intune 公司门户并登录到门户。
-   检查邮件，并且需要使用 Outlook 应用。
-   下载 Outlook 应用。
-   打开 Outlook 应用，并输入注册时使用的凭据。
-   可以接收 Intune 应用保护策略。
-   可以访问电子邮件，Outlook 以及 Intune 应用保护策略。

允许访问公司数据之前，将激活任何 Intune 应用保护策略。 策略可能会提示用户重新启动该应用程序或使用其他的 PIN。 如果为应用程序和平台配置策略，这是这种情况。


### <a name="configuration"></a>配置

**步骤 1：为 Exchange Online 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/01.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。

3. 在中**云应用**，选择**Office 365 Exchange Online**。 

     ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. 在中**条件**，配置**设备平台**并**客户端应用 （预览版）**。 
 
    a. 在中**设备平台**，选择**Android**并**iOS**。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**新式身份验证客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 下**的访问控制**，选择以下选项：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **要求所有选定控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/13.png)



**步骤 2：使用 ActiveSync 为 Exchange Online 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。

3. 在中**云应用**，选择**Office 365 Exchange Online**。 

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. 在中**条件**，配置**客户端应用 （预览版）**。 

    在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**Exchange ActiveSync 客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/92.png)

5. 下**的访问控制**，选择以下选项：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **要求所有选定控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/13.png)




**步骤 3：配置适用于 iOS 和 Android 客户端应用程序的 Intune 应用保护策略**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>为 Exchange Online 和 SharePoint Online 应用基于保护的或基于应用的策略

此方案包括对 Exchange Online 和 SharePoint Online 的访问权限的应用保护基于或已批准应用策略。


### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 配置客户端应用程序是支持应用保护策略要求或已批准的应用要求的应用列表。  
- 使用客户端应用程序满足应用保护策略要求，并且可以接收 Intune 应用保护策略。
- 使用满足支持 Intune 应用保护策略的已批准的应用策略要求的客户端应用程序。
- 打开应用程序以访问电子邮件或文档。
- 打开 Outlook 应用程序并使用 Azure AD 凭据登录。
- 系统会提示安装用于 iOS 的 Microsoft 验证器，或者用于 Android 的 Intune 公司门户，如果它们尚未安装。
- 安装的应用程序和可以返回到 Outlook 应用以继续操作。
- 系统会提示注册的设备。
- 可以接收 Intune 应用保护策略。
- 可以访问电子邮件，Outlook 以及 Intune 应用保护策略。
- 可以访问站点和文档不上的应用保护策略要求的应用，但在已批准的应用要求中列出。

允许访问公司数据之前，任何 Intune 应用保护策略是必需的。 策略可能会提示用户重新启动该应用程序或使用其他的 PIN。 如果为应用程序和平台配置策略，这是这种情况。

**备注**

- 可以使用这种情况下，如果你想要支持这两个应用基于保护和基于应用的条件性访问策略。
- 在此*或*访问第一次之前批准的客户端应用程序要求评估策略，具有应用保护策略要求的应用。

### <a name="configuration"></a>配置

**步骤 1：为 Exchange Online 配置 Azure AD 条件性访问策略**

在此步骤中的条件性访问策略，将配置以下组件：

![条件性访问](./media/app-protection-based-conditional-access/62.png)

1. 输入你的条件性访问策略的名称。

2. 下**分配**，在**用户和组**，选择至少一个用户或组的每个条件性访问策略。

3. 在中**云应用**，选择**Office 365 Exchange Online**。 

     ![条件性访问](./media/app-protection-based-conditional-access/02.png)

4. 在中**条件**，配置**设备平台**并**客户端应用 （预览版）**。 
 
    a. 在中**设备平台**，选择**Android**并**iOS**。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 在中**客户端应用 （预览版）**，选择**移动应用和桌面客户端**并**新式身份验证客户端**。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 下**的访问控制**，选择以下选项：

   - **需要批准的客户端应用**

   - **需要应用保护策略(预览版)**

   - **需要某一已选控件**
 
     ![条件性访问](./media/app-protection-based-conditional-access/12.png)


**步骤 2：配置适用于 iOS 和 Android 客户端应用程序的 Intune 应用保护策略**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

有关详细信息，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。




## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。
- 如果已准备好配置你的环境的条件性访问策略，请参阅[Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。 
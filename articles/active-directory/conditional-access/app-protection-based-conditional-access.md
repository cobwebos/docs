---
title: 如何使用 Azure Active Directory 中条件性访问的云应用访问要求应用保护策略 |Microsoft Docs
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
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288496"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>如何：使用条件性访问 （预览版） 的云应用访问需要应用保护策略

员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 使用 Azure Active Directory (Azure AD) 条件性访问，可以通过限制对云应用到第一次具有应用保护策略的客户端应用的访问来保护公司数据。

本主题说明如何配置条件性访问策略，可能需要应用保护策略之前对数据的访问。

## <a name="overview"></a>概述

借助 [Azure AD 条件访问](overview.md)，可以微调授权用户访问资源的方式。 例如，可以限制为只允许受信任的设备访问云应用程序。

可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据。 Intune 应用程序保护策略不要求使用移动设备管理 (MDM) 解决方案，即无论是否在设备管理解决方案中注册设备，都可以帮助保护公司数据。

Azure Active Directory 条件性访问限制对 Intune 已报告到 Azure AD 为接收应用保护策略的客户端应用程序云应用程序的访问。 例如，可以限制对 Exchange Online 访问到具有 Intune 应用保护策略 Outlook 应用。

在条件性访问术语中，已知这些客户端应用程序可与受保护的策略**应用保护策略**。  

![条件性访问](./media/app-protection-based-conditional-access/05.png)

受保护的客户端应用，请参阅有关的策略列表[应用保护策略要求](technical-reference.md#approved-client-app-requirement)。

你可以组合保护基于应用的条件性访问策略与其他策略，如[基于设备的条件性访问策略](require-managed-devices.md)提供灵活的方式来保护个人和公司设备的数据。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>应用程序保护基于条件性访问要求的优点

类似于符合性报告由 Intune 适用于 iOS 和 Android 的受管理的设备，Intune 现在报表复制到 Azure AD 如果将应用应用保护策略，以便条件性访问可将其用作访问检查。 此新的条件性访问策略**应用保护策略**通过如防止管理员错误来增强安全性：

- 不具有 Intune 许可证的用户
- 不能接收 Intune 应用保护策略的用户
- 未配置以接收策略的 Intune 应用保护策略应用


## <a name="before-you-begin"></a>开始之前

本主题假定你熟悉以下内容：

- [应用保护策略要求](technical-reference.md#app-protection-policy-requirement)技术参考。

- [核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)技术参考。

- [Azure Active Directory 中的条件访问](overview.md)的基本概念。

- 如何[配置条件访问策略](app-based-mfa.md)。


## <a name="prerequisites"></a>必备组件

若要创建应用保护基于条件性访问策略，你必须
- 拥有企业移动性 + 安全性或 Azure Active Directory premium 订阅 + Intune
- 确保用户是否已获得 EMS 或 Azure AD 授权 + Intune
- 确保已在 Intune 接收应用保护策略中配置客户端应用程序
- 请确保用户配置 Intune 以接收 Intune 应用保护策略中

## <a name="app-protection-based-policy-for-exchange-online"></a>为 Exchange Online 应用基于保护策略

此方案包含的应用保护基于条件性访问策略旨在访问 Exchange Online。

### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 使用 iOS 或 Android 上的本地邮件应用程序配置电子邮件，以连接到 Exchange

- 收到一封电子邮件，邮件指示只能通过 Outlook 应用进行访问

- 通过链接下载该应用程序

- 打开 Outlook 应用程序，并使用 Azure AD 凭据登录

- 收到系统提示，要求安装验证器 (iOS) 或公司门户 (Android) 以继续操作

- 安装该应用程序，并可返回 Outlook 应用以继续操作

- 收到系统提示，要求注册设备

- 可以接收 Intune 应用保护策略

- 可访问电子邮件

任何 Intune 应用保护策略必须在应用程序访问公司数据，并且可能会提示用户重新启动该应用程序，请使用其他 pin 码等 （如果已配置为应用程序和平台）。

### <a name="configuration"></a>配置

**步骤 1-为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/01.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。

3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”：

    a. 对于“设备平台”，请选择 Android 和 iOS。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 作为**的访问控制**，你需要准备**需要应用保护策略 （预览版）** 选定。

    ![条件性访问](./media/app-protection-based-conditional-access/05.png)
 

**步骤 2-为 Exchange Online 与 Active Sync (EAS) 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。


3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. **条件：** 对于“条件”，需要配置“客户端应用(预览版)”。 

    a. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/92.png)

    b. 作为**的访问控制**，你需要准备**需要应用保护策略 （预览版）** 选定。

    ![条件性访问](./media/app-protection-based-conditional-access/05.png)


**步骤 3-配置 Intune 应用保护策略的 iOS 和 Android 客户端应用程序**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>为 Exchange Online 的应用保护基于或相容设备策略

此方案包括 Exchange Online 的访问权限的应用保护基于或相容设备条件访问策略。


### <a name="scenario-playbook"></a>方案演练

此方案假定：
 
- 某些用户已注册 （有或没有公司的设备）

- 未使用受保护应用程序向 Azure AD 登记并注册的用户需注册设备，获取资源访问权限

- 已使用受保护应用程序注册的用户无需重新注册设备

- 用户可接收 Intune 应用保护策略，如果不是注册

- 用户可以访问与 Outlook 和 Intune 应用保护策略的电子邮件如果不是注册

- 如果注册了设备，用户可以访问包含 Outlook 电子邮件


### <a name="configuration"></a>配置

**步骤 1-为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/62.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。

3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 

     ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”。 
 
    a. 对于“设备平台”，请选择 Android 和 iOS。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“新式身份验证客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 对于“访问控制”，需选中以下内容：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **需要某一已选控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/11.png)



**步骤 2-为 Exchange Online 与 Active Sync (EAS) 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。

3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. **条件：** 对于“条件”，需要配置“客户端应用”。 

    对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/92.png)

5. 对于“访问控制”，需选中以下内容：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **需要某一已选控件**   
    ![条件性访问](./media/app-protection-based-conditional-access/11.png)



**步骤 3-配置 Intune 应用保护策略的 iOS 和 Android 客户端应用程序**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>为 Exchange Online 应用基于保护和相容设备策略

此方案包含的基于应用的保护和相容设备条件访问策略旨在访问 Exchange Online。


### <a name="scenario-playbook"></a>方案演练

此方案假定用户：
 
-   使用 iOS 或 Android 上的本地邮件应用程序配置电子邮件，以连接到 Exchange
-   收到一封电子邮件，邮件指示需注册设备才能进行访问
-   下载公司门户并登录公司门户
-   检查邮件，并且要求使用 Outlook 应用
-   下载 Outlook 应用
-   打开 Outlook 应用，并输入注册时使用的凭据
-   能够接收以接收 Intune 应用保护策略
-   可以使用 Outlook 和 Intune 应用保护策略访问电子邮件

任何 Intune 应用保护策略激活才能访问公司数据，并可能会提示用户重新启动该应用程序，请使用其他 pin 码等 （如果已配置为应用程序和平台）


### <a name="configuration"></a>配置

**步骤 1-为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/01.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。

3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 

     ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”。 
 
    a. 对于“设备平台”，请选择 Android 和 iOS。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 对于“访问控制”，需选中以下内容：

   - **要求将设备标记为合规**

   - **需要应用保护策略(预览版)**

   - **要求所有选定控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/13.png)



**步骤 2-为 Exchange Online 与 Active Sync (EAS) 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/06.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。

3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 

    ![条件性访问](./media/app-protection-based-conditional-access/07.png)

4. **条件：** 对于“条件”，需要配置“客户端应用(预览版)”。 

    对于“客户端应用(预览版)”，选择“移动应用和桌面客户端”以及“Exchange ActiveSync 客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/92.png)

5. 对于“访问控制”，需选中以下内容：

   - **要求将设备标记为合规**

   - **需要批准的客户端应用 （预览）**

   - **要求所有选定控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/13.png)




**步骤 3-配置 Intune 应用保护策略的 iOS 和 Android 客户端应用程序**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>为 Exchange Online 和 SharePoint Online 应用基于保护的或基于应用的策略

此方案包括对 Exchange Online 和 SharePoint Online 的访问权限的应用保护基于或已批准应用策略。


### <a name="scenario-playbook"></a>方案演练

此方案假定用户：

- 将配置为任一客户端应用程序上的支持应用保护策略要求或已批准的应用要求的应用列表。  
- 用户使用客户端应用程序满足应用保护策略要求可以接收 Intune 应用保护策略
- 用户使用满足支持 Intune 应用保护策略的已批准的应用策略要求的客户端应用程序
- 打开应用程序以访问电子邮件或文档
- 打开 Outlook 应用程序，并使用 Azure AD 凭据登录
- 是系统提示你安装验证器 (iOS) 或公司门户 (Android) 继续 （如果未安装）
- 安装该应用程序，并可返回 Outlook 应用以继续操作
- 收到系统提示，要求注册设备
- 可以接收 Intune 应用保护策略
- 可以使用 Outlook 和 Intune 应用保护策略访问电子邮件
- 是能够访问站点/文档不上的应用保护策略要求的应用，但在已批准的应用要求中列出。

访问公司数据之前，需要进行任何 Intune 应用保护策略，并可能会提示用户重新启动该应用程序，请使用其他 pin 码等 （如果已配置为应用程序和平台）

**备注**

- 可以使用这种情况下，如果你想要支持这两个应用基于保护和基于应用的条件性访问策略。

- 在此*或者*策略中，使用应用程序**应用保护策略**要求来评估的访问权限之前第一个**批准的客户端应用**要求。

### <a name="configuration"></a>配置

**步骤 1-为 Exchange Online 配置 Azure AD 条件性访问策略**

对于此步骤中的条件访问策略，需配置以下各项内容：

![条件性访问](./media/app-protection-based-conditional-access/62.png)

1. 条件访问策略的“名称”。

2. **用户和组**：每个条件访问策略必须选定至少一个用户或组。

3. **云应用：** 对于云应用，需要选择“Office 365 Exchange Online”。 

     ![条件性访问](./media/app-protection-based-conditional-access/02.png)

4. **条件：** 对于“条件”，需要配置“设备平台”和“客户端应用”。 
 
    a. 对于“设备平台”，请选择 Android 和 iOS。

    ![条件性访问](./media/app-protection-based-conditional-access/03.png)

    b. 对于“客户端应用(预览版)”，选择“移动应用和桌面应用”以及“新式身份验证客户端”。

    ![条件性访问](./media/app-protection-based-conditional-access/91.png)

5. 对于“访问控制”，需选中以下内容：

   - **需要批准的客户端应用**

   - **需要应用保护策略(预览版)**

   - **需要某一已选控件**   
 
     ![条件性访问](./media/app-protection-based-conditional-access/12.png)


**步骤 2-配置 Intune 应用保护策略的 iOS 和 Android 客户端应用程序**


![条件性访问](./media/app-protection-based-conditional-access/09.png)

请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)了解详细信息。




## <a name="next-steps"></a>后续步骤

若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。

如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](best-practices.md)。 
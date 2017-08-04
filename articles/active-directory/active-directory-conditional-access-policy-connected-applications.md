---
title: "为连接 Azure Active Directory 的应用程序设置基于设备的条件性访问策略 | Microsoft Docs"
description: "为连接 Azure Active Directory 的应用程序设置基于设备的条件性访问策略。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133
ms.contentlocale: zh-cn
ms.lasthandoff: 12/29/2016

---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>为连接 Azure Active Directory 的应用程序设置基于设备的条件性访问策略
Azure Active Directory (Azure AD) 基于设备的条件性访问有助于保护组织资源，使其不受以下各项情况的影响：

* 未来自知或非托管设备的访问尝试。
* 不符合组织安全策略的设备。

有关条件性访问的概述，请参阅 [Azure Active Directory 条件性访问](active-directory-conditional-access.md)。

可以设置基于设备的条件性访问策略来保护以下应用程序：

* Office 365 SharePoint Online，以保护组织站点和文档
* Office 365 Exchange Online，以保护组织的电子邮件
* 连接 Azure AD 以进行身份验证的软件即服务 (SaaS) 应用程序
* 使用 Azure AD 应用程序代理服务发布的本地应用程序

若要设置基于设备的条件性访问策略，请在 Azure 门户转到目录中的特定应用程序。

  ![Azure 门户目录中的应用程序列表](./media/active-directory-conditional-access-policy-connected-applications/01.png "应用程序")

选择应用程序，然后单击“配置”选项卡，设置条件性访问策略。  

  ![配置应用程序](./media/active-directory-conditional-access-policy-connected-applications/02.png "基于设备的访问规则")

若要设置基于设备的条件访问策略，在“基于设备的访问规则”部分的“启用访问规则”中，选择“开”。

基于设备的条件性访问策略有三个组件：

* **应用于**。 要向其应用此策略的用户范围。
* **设备规则**。 设备访问应用程序所须满足的条件。
* **应用程序强制措施**。 要应用此策略的客户端应用程序（本机与浏览器）。
  
  ![基于设备的访问策略的三个组件](./media/active-directory-conditional-access-policy-connected-applications/03.png "基于设备的访问规则")

## <a name="select-the-users-the-policy-applies-to"></a>选择要向其应用此策略的用户
在**应用于**部分，可以选择要向其应用此策略的用户范围。

创建访问策略应用的用户范围时有两个选项：

* **所有用户**。 将策略应用于所有访问应用程序的用户。
* **组**。 仅将策略应用于属于特定组的用户。

![将策略应用到所有用户或某个组](./media/active-directory-conditional-access-policy-connected-applications/11.png "应用到")

 若要从策略中排除用户，请选择“排除”复选框。 当需要向特定用户授予临时访问应用程序的权限时，这非常有用。 假如某些用户拥有尚未设置条件性访问的设备，可以选择此选项。 这些设备可能尚未注册，或即将不合规。

## <a name="select-the-conditions-that-devices-must-meet"></a>选择设备必须满足的条件
使用**设备规则**设置设备访问应用程序所须满足的条件。

可以为以下设备类型设置基于设备的条件性访问：

* Windows 10 周年更新、Windows 8.1 和 Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2
* iOS 设备（iPad、iPhone）
* Android 设备

即将推出针对 Mac 的支持。

  ![将策略应用到设备](./media/active-directory-conditional-access-policy-connected-applications/04.png "应用程序")

> [!NOTE]
> 有关加入域和加入 Azure AD 的设备之间的差异的信息，请参阅 [Using Windows 10 devices in your workplace](active-directory-azureadjoin-windows10-devices.md)（在工作区中使用 Windows 10 设备）。
> 
> 

有两个设备规则选项：

* **所有设备都必须合规**。 访问应用程序的所有设备平台都必须合规。 对于在不支持基于设备的条件性访问的平台上运行的设备，会拒绝其访问。
* **仅所选设备必须合规**。 仅特定设备平台必须合规。 其他平台或其他可以访问该应用程序的平台具有访问权限。
  
  ![设置设备规则的范围](./media/active-directory-conditional-access-policy-connected-applications/05.png "应用程序")

对于加入 Azure AD 的设备，如果 Intune 或与 Azure AD 集成的第三方移动设备管理系统在目录中将其标记为“合规”，则这些设备合规。

如果满足以下条件，则加入域的设备合规：

* 注册到 Azure AD。 许多组织将加入域的设备视为受信任的设备。
* 由 System Center Configuration Manager 在 Azure AD 中标记为“合规”。
  
  ![已加入域的合规设备](./media/active-directory-conditional-access-policy-connected-applications/06.png "设备规则")

对于 Windows 个人设备，如果 Intune 或与 Azure AD 集成的第三方移动设备管理系统在目录中将其标记为“合规”，则这些设备合规。

对于非 Windows 设备，如果 Intune 在目录中将其标记为“合规”，则这些设备合规。

> [!NOTE]
> 若要深入了解如何在不同的管理系统中针对设备合规性设置 Azure AD，请参阅 [Azure Active Directory 条件性访问](active-directory-conditional-access.md)。
> 
> 

可以为基于设备的访问策略选择一个或多个设备平台。 包括 Android、iOS、Windows Mobile（Windows 8.1 手机和平板电脑）以及 Windows（所有其他 Windows 设备，包括所有 Windows 10 设备）。
仅在所选平台上进行策略评估。 如果尝试访问的设备未运行选定的平台之一，而用户具有访问权限，则设备能够访问应用程序。 不会评估设备策略。

![为设备规则选择平台](./media/active-directory-conditional-access-policy-connected-applications/07.png "设备规则")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>为一种类型的应用程序设置策略评估
在**应用程序强制措施**部分，设置策略将为其评估用户或设备访问的应用程序的类型。

应用程序类型包括两个选项：

* 浏览器和本机应用程序
* 仅本机应用程序

![选择浏览器或本机应用程序](./media/active-directory-conditional-access-policy-connected-applications/08.png "应用程序")

要强制实施应用程序的访问策略，请选择“适用于浏览器和本机应用程序”。 然后，可以包括：

* 浏览器（例如 Windows 10 中的 Microsoft Edge 或 iOS 中的 Safari）。
* 在任何平台上使用 Active Directory 身份验证库 (ADAL) 或在 Windows 10 中使用 WebAccountManager (WAM) API 的应用程序。

> [!NOTE]
> 若要深入了解浏览器支持和用户访问基于设备、受证书颁发机构保护的应用程序的其他注意事项，请参阅 [Azure Active Directory 条件性访问](active-directory-conditional-access.md)。
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>有助于保护来自基于 Exchange ActiveSync 的应用程序的电子邮件访问
在 Office 365 Exchange Online 应用程序中，可以使用 Exchange ActiveSync 阻止电子邮件访问基于 Exchange ActiveSync 的邮件应用程序。

![Exchange ActiveSync 合规性选项](./media/active-directory-conditional-access-policy-connected-applications/09.png "应用程序")

![需要兼容设备才可访问电子邮件](./media/active-directory-conditional-access-policy-connected-applications/10.png "应用程序")

## <a name="next-steps"></a>后续步骤
* [Azure Active Directory 条件访问](active-directory-conditional-access.md)



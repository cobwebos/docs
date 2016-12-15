---
title: "Azure Active Directory 条件性访问 | Microsoft Docs"
description: "使用 Azure Active Directory 中的条件性访问控制以检查对应用程序的访问进行身份验证时的特定条件。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2cd4a7536bcdde717221a8f2d25eadadee03dd4d


---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的条件性访问
Azure Active Directory (Azure AD) 条件性访问中的控制功能是帮助保护云中和本地的资源的一种简单方式。 条件性访问策略如多重身份验证可帮助抵御凭据被盗和被骗的风险。 其他条件性访问策略可帮助维持组织的数据的安全。 例如，除了要求提供凭据，你可能还有一个策略，即只有在移动设备管理系统中（如 Microsoft Intune）注册的设备才可以访问你组织的敏感服务。

## <a name="prerequisites"></a>先决条件
Azure AD 条件性访问是 [Azure Active Directory Premium](http://www.microsoft.com/identity) 的一项功能。 访问应用了条件性访问策略的应用程序的所有用户都必须拥有 Azure AD Premium 许可证。 可以在[未经授权用户报表](https://aka.ms/utc5ix)中了解有关许可证要求的更多信息。

## <a name="how-is-conditional-access-control-enforced"></a>如何强制实施条件性访问控制？
启用条件性访问控制后，Azure AD 检查为用户访问应用程序设置的特定条件。 满足访问要求后，用户通过身份验证并可以访问应用程序。  

![条件性访问概述](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>条件
以下是可以包含在条件性访问策略中的条件：

* **组成员身份**。 根据组中的成员身份控制用户的访问。
* **位置**。 使用用户的位置触发多重身份验证，当用户不在受信任的网络中时使用阻止控件。
* **设备平台**。 使用设备平台（如 iOS、Android、Windows Mobile 和 Windows）作为应用策略的条件。
* **启用设备**。 在评估设备策略过程中会验证设备是启用还是禁用状态。 禁用目录中丢失或被盗的设备后，该设备不再满足策略要求。
* **登录和用户的风险**。 可以将 [Azure AD Identity Protection](active-directory-identityprotection.md) 用于条件性访问风险策略。 条件性访问风险策略可根据风险事件和异常登录活动来为组织提供高级保护。

## <a name="controls"></a>控制
以下是可用于执行条件性访问策略的控件：

* **多重身份验证**。 可以要求在多重身份验证过程中进行强身份验证。 可以通过以下方式使用多重身份验证：使用 Azure 多重身份验证，或使用与 Active Directory 联合服务 (AD FS) 结合的本地多重身份验证提供程序。 对于可能已获有效用户的凭据的访问权限的未授权用户，使用多重身份验证可帮助防止其访问你的资源。
* **阻止**。 可以应用如用户位置的条件来阻止用户访问。 例如，当用户不在受信任的网络中时阻止访问。
* **合规的设备**。 可以在设备级别设置条件性访问策略。 你可以设置一个策略，以便只有已加入域的计算机或在移动设备管理应用程序中注册的移动设备才可以访问你组织的资源。 例如，可以使用 Intune 来检查设备的合规性，然后在用户尝试访问应用程序时将其报告给 Azure AD 以执行策略。 有关如何使用 Intune 保护应用和数据的详细指南，请参阅[使用 Microsoft Intune 保护应用和数据](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune)。 此外还可以使用 Intune 保护丢失或被盗设备的数据。 有关详细信息，请参阅[使用 Microsoft Intune 的完全擦除或选择性擦除保护数据](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)。

## <a name="applications"></a>应用程序
可以在应用程序级别执行条件性访问策略。 设置云中或本地应用程序和服务的访问级别。 直接将策略应用于网站或服务。 对浏览器的访问和对访问服务的应用程序的访问执行策略。

## <a name="device-based-conditional-access"></a>基于设备的条件访问
可以限制在 Azure AD 中注册且满足特定条件的设备对应用程序的访问。 基于设备的条件性访问可以防止尝试从以下设备访问资源的用户访问组织的资源：

* 未知/非托管设备。
* 不符合组织设置的安全策略的设备。

可以根据以下要求设置策略：

* **加入域的设备**。 设置策略来仅限已加入本地 Active Directory 域并已在 Azure AD 中注册的设备具有访问权限。 此策略可应用于 Windows 台式机、笔记本电脑和企业平板电脑。
  有关如何设置已加入域的设备在 Azure AD 中的自动注册的详细信息，请参阅[对已加入域的 Windows 设备在 Azure Active Directory 中的自动注册进行设置](active-directory-conditional-access-automatic-device-registration-setup.md)。
* **合规的设备**。 设置策略来仅限在管理系统目录中标记为“兼容”的设备具有访问权限。 此策略可确保只有符合安全策略（例如，强制对设备实施文件加密）的设备可以访问。 此策略可用于限制从以下设备进行的访问：
  
  * **加入域的 Windows 设备**。 由混合配置中部署的 System Center Configuration Manager（位于当前分支）管理。
  * **Windows 10 Mobile 工作或个人设备**。 由 Intune 或支持的第三方移动设备管理系统管理。
  * **iOS 和 Android 设备**。 由 Intune 管理。

对于受基于设备的证书颁发机构策略保护的应用程序，用户必须从满足此策略的要求的设备访问此应用程序。 如果在不满足策略要求的设备上进行尝试，那么访问将被拒绝。

有关如何在 Azure AD 中配置基于设备的证书颁发机构策略的信息，请参阅[为与 Azure Active Directory 连接的应用程序设置基于设备的条件性访问策略](active-directory-conditional-access-policy-connected-applications.md)。

## <a name="resources"></a>资源
请参阅以下资源类别和文章，以了解有关为组织设置条件性访问的更多信息。

### <a name="multi-factor-authentication-and-location-policies"></a>多重身份验证和位置策略
* [基于组、位置和多重身份验证策略实现对与 Azure AD 连接的应用的条件性访问入门](active-directory-conditional-access-azuread-connected-apps.md)
* [支持的应用程序](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>基于设备的条件访问
* [设置基于设备的条件性访问策略以实现对与 Azure Active Directory 连接的应用程序的访问控制](active-directory-conditional-access-policy-connected-applications.md)
* [对已加入域的 Windows 设备在 Azure Active Directory 中的自动注册进行设置](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Azure Active Directory 访问问题的疑难解答](active-directory-conditional-access-device-remediation.md)
* [使用 Microsoft Intune 保护丢失或被盗的设备上的数据](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>根据登录风险保护资源
* [Azure AD Identity Protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>后续步骤
* [条件性访问常见问题解答](active-directory-conditional-faqs.md)
* [技术参考](active-directory-conditional-access-technical-reference.md)




<!--HONumber=Nov16_HO3-->



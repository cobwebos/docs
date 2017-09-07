---
title: "Azure Active Directory 条件性访问 | Microsoft 文档"
description: "使用 Azure Active Directory 中的条件性访问控制检查对应用程序的访问进行身份验证时的特定条件。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: c97f05caec4c302c847e2297d136c6614e82fd93
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的条件性访问

在移动优先、云优先的世界中，使用 Azure Active Directory 可以实现从任意位置单一登录到设备、应用和服务。 随着 BYOD 等设备、脱离企业网络的办公和第三方 SaaS 应用的普及，IT 专业人员面临着两个对立的目标：

- 使最终用户能够随时随地保持高效的工作
- 随时保持企业资产

为了提高工作效率，Azure Active Directory 提供多种选项来让用户访问企业资产。 Azure Active Directory 通过应用程序访问管理确保只有*适当的人员*才可以访问应用程序。 如何以更大的力度控制适当的人员在特定的条件下如何访问资源？ 对于*适当的人员*，在某些情况下如何阻止其访问特定的应用？ 例如，有时可以允许适当的人员通过受信任的网络访问特定的应用，但不允许他们通过不受信任的网络访问这些应用。 使用条件性访问可以解决这些问题。

条件访问是 Azure Active Directory 的一项功能，可用于根据特定条件针对环境中的应用强制实施访问控制。 通过这种控制，可以将其他要求关联到访问，或者阻止访问。 条件性访问的实现基于策略。 基于策略的方法可以简化配置体验，因为它遵循访问要求方面的考虑因素。  

通常，我们会使用基于以下模式的语句来定义访问要求：

![控制](./media/active-directory-conditional-access-azure-portal/10.png)

将两处出现的词语“这种”替换为实际信息后，便可以获得一个可能让你觉得熟悉的示例策略语句：

*如何分包商尝试从不受信任的网络访问我们的云应用，则阻止访问。*

上述策略语句突显了条件性访问的能力。 虽然可以使用条件性访问让分包商（**谁**）对云应用进行基本的访问，但还可以定义在哪种条件下可以进行这种访问（**如何**）。

在 Azure Active Directory 条件性访问的上下文中：

- “**如何发生这种情况**”称为**条件语句**
- “**则执行这种操作**”称为**控制**

![控制](./media/active-directory-conditional-access-azure-portal/11.png)

条件语句与控制的组合表示一种条件性访问策略。

![控制](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>控制

在条件性访问策略中，控制定义了在满足某个条件语句时应采取的措施。  
使用控制可以阻止访问，或者在满足其他要求的情况下允许访问。
配置允许访问的策略时，至少需要选择一项要求。   

### <a name="grant-controls"></a>授权控制
Azure Active Directory 的当前实现允许配置以下授权控制要求：

![控制](./media/active-directory-conditional-access-azure-portal/05.png)

- **多重身份验证** - 可以要求在多重身份验证过程中进行强身份验证。 对于提供程序，可以结合 Active Directory 联合服务 (AD FS) 使用 Azure 多重身份验证或本地多重身份验证提供程序。 对于可能已获有效用户的凭据的访问权限的未授权用户，使用多重身份验证可帮助防止其访问资源。

- 合规设备 - 可以配置基于设备的条件性访问策略。 基于设备的条件性访问策略旨在仅从受信任的设备授予对已配置资源的访问权限。 要求兼容设备是一个必须定义什么是可信任设备的选项。 有关详细信息，请参阅[配置 Azure Active Directory 基于设备的条件性访问策略](active-directory-conditional-access-policy-connected-applications.md)。

- 已加入域的设备 – 要求加入域的设备是你必须配置基于设备的条件性访问策略的另一个选项。 此要求是指加入本地 Active Directory 的 Windows 台式机、笔记本电脑和企业平板电脑。 有关详细信息，请参阅[配置 Azure Active Directory 基于设备的条件性访问策略](active-directory-conditional-access-policy-connected-applications.md)。

如果已选择多项控制，还可以配置在处理策略时是否所有这些控制都是必需的。

![控制](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>会话控制
可以通过会话控制限制云应用中的体验。 会话控制由云应用强制实施，取决于由 Azure AD 提供给应用的有关会话的其他信息。

![控制](./media/active-directory-conditional-access-azure-portal/31.png)

#### <a name="use-app-enforced-restrictions"></a>使用应用所强制实施的限制
可以使用此控制要求 Azure AD 将设备信息传递给云应用。 这样是为了让云应用了解用户是否来自合规的设备或已加入域的设备。 目前仅用作云应用的 SharePoint 支持此控制。 SharePoint 在收到设备信息后，会根据设备状态为用户通过受限的体验或完整的体验。
若要详细了解如何使用 SharePoint 进行受限访问设置，请参阅[控制从非托管设备的访问权限](https://aka.ms/spolimitedaccessdocs)。

## <a name="condition-statement"></a>条件语句

上一部分介绍了可以使用哪些支持的选项，以控制形式来阻止或限制对资源的访问。 在条件性访问策略中，可以定义需要满足哪种条件，才以条件语句的形式应用控制。  

可在条件语句中包含以下分配：

![控制](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>谁？

在配置条件性访问策略时，需要选择要向其应用策略的用户或组。 在很多情况下，我们想要对特定的一组用户应用控制。 在条件语句中，可以通过选择要向其应用策略的所需用户和组来定义此设置。 如果需要，还可以通过豁免的方式从策略中明确排除一组用户。  

![控制](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>什么？

在配置条件性访问策略时，需要选择要向其应用策略的云应用。
通常，从保护的角度来看，环境中的某些应用需要的关注程度要比其他应用多一些。 举例而言，这种控制会影响到有权访问敏感数据的应用。
通过选择云应用，可以定义要将策略适用到的云应用范围。 如果需要，还可以从策略中明确排除一组应用。

![控制](./media/active-directory-conditional-access-azure-portal/09.png)

对于可以在条件性访问策略中使用的完整云应用列表，请参阅 [Azure Active Directory 条件性访问技术参考](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments)。

### <a name="how"></a>如何进行？

只要对应用的访问是根据可控的条件执行的，可能就不需要对用户访问云应用的方式实施附加控制。 但是，如何对云应用的访问是通过不受信任的网络或者不合规的设备执行的，则情况可能就有所不同。 在条件语句中，可以定义特定的访问条件并附带一些要求来控制访问应用的执行方式。

![条件](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="conditions"></a>条件

在 Azure Active Directory 的当前实现中，可针对以下方面定义条件：

- 登录风险
- 设备平台
- 位置
- 客户端应用

![条件](./media/active-directory-conditional-access-azure-portal/21.png)

### <a name="sign-in-risk"></a>登录风险

登录风险是 Azure Active Directory 所使用的一个对象，用于跟踪不是由用户帐户合法拥有者进行登录尝试的可能性。 在此对象中，可能性（高、中、低）以名为[登录风险级别](active-directory-reporting-risk-events.md#risk-level)的属性的形式存储。 在用户登录期间，如果 Azure Active Directory 检测到登录风险，则会生成此对象。 有关更多详细信息，请参阅[风险登录](active-directory-identityprotection.md#risky-sign-ins)。  
可以将计算得到的登录风险级别用作条件性访问策略中的条件。 

![条件](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>设备平台

设备平台根据设备上运行的操作系统来定义特征：

- Android
- iOS
- Windows Phone
- Windows
- macOS（预览）。 

![条件](./media/active-directory-conditional-access-azure-portal/02.png)

可以定义要在策略中包含以及排除的设备平台。  
要在策略中使用设备平台，请先将“配置”开关更改为“是”，然后选择要将策略应用到的所有或单个设备平台。 如果选择单个设备平台，该策略只对这些平台产生影响。 在这种情况下，登录到其他受支持的平台不受该策略的影响。


### <a name="locations"></a>位置

位置由用于连接到 Azure Active Directory 的客户端的 IP 地址标识。 设置此条件需要熟悉命名位置和受 MFA 信任的 IP。  

命名位置是 Azure Active Directory 的一项功能，用于标记组织中受信任的 IP 地址范围。 在你的环境中，可以在[风险事件](active-directory-reporting-risk-events.md)检测以及条件性访问的上下文中使用命名位置。 有关在 Azure Active Directory 中配置命名位置的更多详细信息，请参阅 [Azure Active Directory 中的命名位置](active-directory-named-locations.md)。

可配置的位置数目取决于 Azure AD 中相关对象的大小。 可以配置：
 
 - 一个命名位置最多可以有 500 个 IP 范围
 - 最多可有 60 个命名位置（预览版），其中每个都分配有一个 IP 范围 


受 MFA 信任的 IP 是多重身份验证的一项功能，可用于定义代表组织本地 Intranet 的受信任 IP 范围。 配置位置条件时，可以使用受信任的 IP 来区分从组织网络进行的连接以及从其他位置进行的连接。 有关详细信息，请参阅[受信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。  



可以包含所有位置或所有受信任的 IP，也可以排除所有受信任的 IP。

![条件](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>客户端应用

客户端应用可以是用于连接 Azure Active Directory 的广泛意义上的应用（Web 浏览器、移动应用或桌面客户端），也可以是专门选择的 Exchange Active Sync。  
旧式身份验证是指使用基本身份验证的客户端，例如，不使用新式身份验证的旧式 Office 客户端。 旧式身份验证目前不支持条件性访问。

![条件](./media/active-directory-conditional-access-azure-portal/04.png)


对于可以在条件性访问策略中使用的完整客户端应用列表，请参阅 [Azure Active Directory 条件性访问技术参考](active-directory-conditional-access-technical-reference.md#client-apps-conditions)。



## <a name="common-scenarios"></a>常见方案

### <a name="requiring-multi-factor-authentication-for-apps"></a>要求对应用使用多重身份验证

在许多环境中，某些应用所需的保护级别要比其他应用要高一些。
例如，有权访问敏感数据的应用就需要更高的保护级别。
如果想要为这些应用添加另一层保护，可以配置条件访问策略，要求访问这些应用的用户执行多重身份验证。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>通过不受信任的网络访问时要求执行多重身份验证

此方案与前一种方案类似，两者都提出了多重身份验证的要求。
但是，主要区别在于此项要求的条件。  
前一种方案的重心是有权访问敏感数据的应用，而本方案的重心则是受信任的位置。  
换而言之，如果用户通过不受信任的网络访问应用，可以要求其进行多重身份验证。


### <a name="only-trusted-devices-can-access-office-365-services"></a>只允许受信任的设备访问 Office 365 服务

如果在环境中使用 Intune，马上就可以在 Azure 控制台中开始使用条件性访问策略接口。

许多 Intune 客户都在使用条件性访问来确保只有受信任的设备可以访问 Office 365 服务。 这意味着，访问这些服务的移动设备已注册到 Intune 并满足合规策略要求，Windows 电脑已加入本地域。 一项重要改进是不需要针对每个 Office 365 服务设置相同的策略。  创建新策略时，可以配置云应用，以包含想要使用条件性访问保护的每个 O365 应用。

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 

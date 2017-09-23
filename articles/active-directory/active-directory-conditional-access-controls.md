---
title: "Azure Active Directory 条件访问中的控制 | Microsoft Docs"
description: "了解 Azure Active Directory 条件访问中的控制的工作原理。"
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
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 0f7d847c98e790c542f3a3e666b9a887099a6cbc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---

# <a name="controls-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件访问中的控制 

使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)，可以控制授权用户访问云应用程序的方式。 在条件访问策略中，定义在特定条件下（“出现这种情况时”）的响应（“这样做”）。 在条件访问的上下文中， 

- “**如何发生这种情况**”称为**条件语句**
- “**则执行这种操作**”称为**控制**

![控制](./media/active-directory-conditional-access-controls/11.png)

条件语句与控制的组合表示一种条件性访问策略。

![控制](./media/active-directory-conditional-access-controls/12.png)

每个控制要么限制人员或系统必须满足哪些要求才能登录，要么限制用户在登录后可以执行哪些操作。 

访问控制分为两种类型： 

- **授权控制** - 旨在限制访问

- **会话控制** - 旨在限制可以在会话中执行的操作

本主题介绍了 Azure AD 条件访问中的各种控制。 

## <a name="grant-controls"></a>授权控制

使用授权控制，可以完全阻止访问，也可以选择所需的控制，限制为只有满足其他要求才能访问。 如果有多个控制，可以要求：

- 满足所有选定控制 (AND) 
- 满足一个选定控制 (OR)

![控制](./media/active-directory-conditional-access-controls/51.png)



### <a name="multi-factor-authentication"></a>多重身份验证

此控制可用于要求必须通过多重身份验证，才能访问指定的云应用程序。 此控制支持以下多重身份验证提供程序： 

- Azure 多重身份验证 

- 本地多重身份验证提供程序，与 Active Directory 联合身份验证服务 (AD FS) 结合使用。
 
使用多重身份验证有助于保护资源，使其免遭可能已有权访问有效用户的主要凭据的未授权用户访问。



### <a name="compliant-device"></a>合规的设备

可以配置基于设备的条件访问策略。 基于设备的条件性访问策略旨在仅从受信任的设备授予对已配置资源的访问权限。 要求兼容设备是一个必须定义什么是可信任设备的选项。 有关详细信息，请参阅[设置 Azure Active Directory 基于设备的条件访问策略](active-directory-conditional-access-policy-connected-applications.md)。

### <a name="domain-joined-device"></a>已加入域的设备

若要配置基于设备的条件访问策略，还可以要求必须为已加入域的设备。 此要求是指已加入本地 Active Directory 的 Windows 台式机、笔记本电脑和企业平板电脑。 有关详细信息，请参阅[设置 Azure Active Directory 基于设备的条件访问策略](active-directory-conditional-access-policy-connected-applications.md)。





### <a name="approved-client-app"></a>核准客户端应用程序

由于员工使用移动设备执行个人和工作任务，因此可能需要能够保护设备访问的公司数据，即使这些设备不受你管理，也不例外。
可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据，而不受任何移动设备管理 (MDM) 解决方案的影响。


利用核准客户端应用程序，可以要求客户端应用程序必须支持 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，才能访问云应用程序。 例如，可以限制为只有 Outlook 应用程序，才能访问 Exchange Online。 要求必须为核准客户端应用程序的条件访问策略亦称为[基于应用程序的条件访问策略](active-directory-conditional-access-mam.md)。 有关支持的核准客户端应用程序列表，请参阅[核准客户端应用程序要求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)。



## <a name="session-controls"></a>会话控制

通过会话控制，可以限制云应用程序中的体验。 会话控制由云应用强制实施，取决于由 Azure AD 提供给应用的有关会话的其他信息。

![控制](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>使用应用所强制实施的限制

可以使用此控制要求 Azure AD 将设备信息传递给云应用。 这样是为了让云应用了解用户是否来自合规的设备或已加入域的设备。 目前仅用作云应用的 SharePoint 支持此控制。 SharePoint 在收到设备信息后，会根据设备状态为用户通过受限的体验或完整的体验。
若要详细了解如何使用 SharePoint 进行受限访问设置，请参阅[控制从非托管设备的访问权限](https://aka.ms/spolimitedaccessdocs)。



## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅 [Azure Active Directory 中的条件访问入门](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 


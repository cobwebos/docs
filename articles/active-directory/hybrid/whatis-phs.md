---
title: 什么是与 Azure AD 的密码哈希同步？ | Microsoft Docs
description: 介绍了密码哈希同步。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23f88a783ca257090fc607cc9a16ec457c7e9c8c
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016582"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>什么是与 Azure AD 的密码哈希同步？
密码哈希同步是用来实现混合标识的登录方法之一。 Azure AD Connect 将用户密码的哈希从本地 Active Directory 实例同步到基于云的 Azure AD 实例。

密码哈希同步是由 Azure AD Connect 同步实现的目录同步功能的扩展。可以使用此功能登录到 Azure AD 服务（例如 Microsoft 365）。 登录到该服务时使用的密码与登录到本地 Active Directory 实例时使用的密码相同。

![什么是 Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

密码哈希同步通过将用户需要维护的密码数目减少为一个来提供帮助。 密码哈希同步可以：

* 提升用户的生产力。
* 减少技术支持成本。  

密码哈希同步还为混合帐户启用了[泄露凭据监测](../identity-protection/concept-identity-protection-risks.md#user-risk)。 Microsoft 与暗网研究人员和执法机构合作，查找公开提供的用户名/密码对。 如果这些对中的任何一对与我们的用户匹配，则关联的帐户将被移动到高风险区域。 

>[!NOTE]
> 只有在启用 PHS 后找到的新泄漏凭据将针对你的租户进行处理。 不会对先前找到的凭据对执行验证。


（可选）如果决定使用[通过 Active Directory 联合身份验证服务 (AD FS) 进行的联合身份验证](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)作为登录方法，则可以将密码哈希同步设置为备用身份验证方式。

若要在环境中使用密码哈希同步，需要：

* 安装 Azure AD Connect。  
* 配置本地 Azure Active Directory 实例与 Azure Active Directory 实例之间的目录同步。
* 启用密码哈希同步。



有关详细信息，请参阅[什么是混合标识？](whatis-hybrid-identity.md)




## <a name="next-steps"></a>后续步骤

- [什么是混合标识？](whatis-hybrid-identity.md)
- [什么是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md)
- [什么是直通身份验证 (PTA)？](how-to-connect-pta.md)
- [什么是联合身份验证？](whatis-fed.md)
- [什么是单一登录？](how-to-connect-sso.md)
- [密码哈希同步的工作原理](how-to-connect-password-hash-synchronization.md)

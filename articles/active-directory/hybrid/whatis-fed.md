---
title: 什么是与 Azure AD 的联合？ | Microsoft Docs
description: 介绍了与 Azure AD 的联合。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7a5b186ebc98babe1baacc9f56f39dc8fbc093
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80049371"
---
# <a name="what-is-federation-with-azure-ad"></a>什么是与 Azure AD 的联合？

联合是已建立信任的域的集合。 信任级别可能有所不同，但通常包括身份验证，几乎始终包括授权。 典型的联合可能包括为了对一组资源进行共享访问而建立信任的许多组织。

你可以将本地环境与 Azure AD 进行联合，并使用此联合进行身份验证和授权。  此登录方法可确保所有用户身份验证都在本地进行。  此方法允许管理员实施更严格的访问控制。 可以使用 AD FS 和 PingFederate 进行联合身份验证。

![联合标识](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> 如果决定使用 Active Directory 联合身份验证服务 (AD FS) 进行联合身份验证，则可以选择性地设置密码哈希同步，作为在 AD FS 基础结构发生故障时的备用身份验证方式。


## <a name="next-steps"></a>后续步骤

- [什么是混合标识？](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [什么是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md)
- [什么是密码哈希同步？](whatis-phs.md)
- [什么是联合身份验证？](whatis-fed.md)
- [什么是单一登录？](how-to-connect-sso.md)
- [联合身份验证的工作原理](how-to-connect-fed-whatis.md)
- [使用 PingFederate 进行联合身份验证](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

---
title: 什么是 Azure AD Connect 和 Connect Health？ | Microsoft Docs
description: 介绍了用来通过 Azure AD 同步和监视本地环境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1c18200bb36b75a07d7b26e3ea0016ec35efdd87
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460487"
---
# <a name="what-is-azure-ad-connect"></a>什么是 Azure AD Connect？

Azure AD Connect 专用于满足和完成混合标识目标的 Microsoft 工具。  它提供以下功能：
    
- [密码哈希同步](whatis-phs.md) - 一种登录方法，它将用户的本地 AD 密码与 Azure AD 进行同步。
- [直通身份验证](how-to-connect-pta.md) - 另一种登录方法，它允许用户在本地和云中使用相同的密码，但不要求额外提供联合环境的基础结构。
- [联合身份验证集成](how-to-connect-fed-whatis.md) - 联合身份验证是 Azure AD Connect 的可选部件，可用于使用本地 AD FS 基础结构配置混合环境。 它还提供了 AD FS 管理功能，例如证书续订和其他 AD FS 服务器部署。
- [同步](how-to-connect-sync-whatis.md) - 负责创建用户、组和其他对象。  另外，它还负责确保本地用户和组的标识信息与云匹配。  此同步还包括密码哈希。
-   [运行状况监视](whatis-hybrid-identity-health.md) - Azure AD Connect Health 提供可靠监视，并在 Azure 门户中提供一个中心位置，用于查看此活动。 


![什么是 Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>什么是 Azure AD Connect Health？

Azure Active Directory (Azure AD) Connect Health 为本地标识基础结构提供可靠的监视功能。 它可以用于维护到 Office 365 和 Microsoft Online Services 的可靠连接。  此可靠性是通过针对关键标识组件提供监视功能来实现的。 另外，它还使有关这些组件的关键数据点可轻松访问。

这些信息显示在 [Azure AD Connect Health 门户](https://aka.ms/aadconnecthealth)中。 可以使用 Azure AD Connect Health 门户来查看警报、性能监视、使用情况分析和其他信息。 Azure AD Connect Health 在一个集中的位置提供重要标识组件的运行状况单一可重用功能区。

![什么是 Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>为何使用 Azure AD Connect？
将本地目录与 Azure AD 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。 用户和组织可以得到以下好处：

* 用户可以使用单个标识来访问本地应用程序和云服务，例如 Office 365。
* 单个工具即可提供轻松同步和登录的部署体验。
* 为方案提供最新功能。 Azure AD Connect 取代了 DirSync 和 Azure AD Sync 等早期版本的标识集成工具。有关详细信息，请参阅 [混合标识目录集成工具比较](plan-hybrid-identity-design-considerations-tools-comparison.md)。

## <a name="why-use-azure-ad-connect-health"></a>为何使用 Azure AD Connect Health？
使用 Azure AD 时，用户的工作效率更高，因为可以通过一个通用标识来访问云和本地资源。 确保环境可靠以便用户可以访问这些资源已成为一个难题。  Azure AD Connect Health 可帮助你监视和深入了解本地标识基础结构，从而确保此环境的可靠性。 这种方法就像在每个本地标识服务器上安装代理那样简单。


## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 

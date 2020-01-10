---
title: 什么是 Azure AD Connect 和 Connect Health？ | Microsoft Docs
description: 介绍了用来通过 Azure AD 同步和监视本地环境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c984faf22b8e4cd499a5588f45d7072610f33544
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767919"
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
* 为方案提供最新功能。 Azure AD Connect 替换旧版本的标识集成工具（例如 DirSync 和 Azure AD Sync）。有关详细信息，请参阅[混合标识目录集成工具比较](plan-hybrid-identity-design-considerations-tools-comparison.md)。

## <a name="why-use-azure-ad-connect-health"></a>为何使用 Azure AD Connect Health？
使用 Azure AD 时，用户的工作效率更高，因为可以通过一个通用标识来访问云和本地资源。 确保环境可靠以便用户可以访问这些资源已成为一个难题。  Azure AD Connect Health 可帮助你监视和深入了解本地标识基础结构，从而确保此环境的可靠性。 这种方法就像在每个本地标识服务器上安装代理那样简单。

适用于 AD FS 的 Azure AD Connect Health 支持 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 中的 AD FS 2.0。 它还支持监视为 Extranet 访问提供身份验证支持的 AD FS 代理或 Web 应用程序代理服务器。 轻松快速地安装 Health 代理后，适用于 AD FS 的 Azure AD Connect Health 可提供以下一组重要功能：

主要优点和最佳做法：

|主要优势|最佳实践|
|-----|-----|
|增强的安全性|[Extranet 锁定趋势](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[失败的登录报告](how-to-connect-health-adfs-risky-ip.md)</br>[符合隐私标准](reference-connect-health-user-privacy.md)|
|[出现严重 ADFS 系统问题](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)时会收到通知|服务器配置和可用性</br>[性能和连接](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>定期维护|
|易于部署和管理|[快速的代理安装](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>代理自动升级到最新版本</br>数据在几分钟后便可出现在门户中|
丰富的[使用情况指标](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|排名靠前的应用程序使用情况</br>网络位置和 TCP 连接</br>每个服务器的令牌请求|
|出色的用户体验|Azure 门户中的仪表板方式</br>[通过电子邮件提供警报](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>使用 Azure AD Connect 的许可证要求

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 的许可要求
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 

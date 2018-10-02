---
title: 将 Active Directory 连接到 Azure Active Directory | Microsoft Docs
description: Azure AD Connect 会将本地目录与 Azure Active Directory 集成。 这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序提供一个通用标识。
keywords: Azure AD Connect 介绍, Azure AD Connect 概述, 什么是 Azure AD Connect, 安装 active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181765"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>混合标识和 Microsoft 的标识解决方案
如今，企业和公司越来越成为本地应用程序和云应用程序的混合体。  如果应用程序和用户需要访问位于本地和云中的那些应用程序，这将成为一个具有挑战性的场景。

Microsoft 的标识解决方案跨越本地和基于云的功能，创建单一用户标识对所有资源进行身份验证和授权，而不考虑其位置。 我们称此为混合标识。

## <a name="what-is-azure-ad-connect"></a>什么是 Azure AD Connect？

Azure AD Connect 是设计用来满足和完成你的混合标识目标的 Microsoft 工具。  这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序的用户提供一个通用标识。  它提供以下功能：
    
- [同步](how-to-connect-sync-whatis.md) - 此组件负责创建用户、组和其他对象。 它还负责确保本地用户和组的标识信息与云匹配。  它负责将密码哈希与 Azure AD 进行同步。
-   [AD FS 和联合身份验证集成](how-to-connect-fed-whatis.md) - 联合身份验证是 Azure AD Connect 的可选部件，可用于使用本地 AD FS 基础结构配置混合环境。 它还提供了 AD FS 管理功能，例如证书续订和其他 AD FS 服务器部署。
-   [直通身份验证](how-to-connect-pta.md) - 另一个可选组件，它允许用户在本地和云中使用相同的密码，但不要求额外提供联合环境的基础结构。
-   [运行状况监视](whatis-hybrid-identity-health.md) - Azure AD Connect Health 提供可靠监视，并在 Azure 门户中提供一个中心位置，用于查看此活动。 


![什么是 Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>什么是 Azure AD Connect Health？

Azure Active Directory (Azure AD) Connect Health 可帮助你监视和深入了解本地标识基础结构和同步服务。 它针对重要的标识组件 - 例如 Active Directory 联合身份验证服务 (AD FS) 服务器、Azure AD Connect 服务器（也称为同步引擎）、Active Directory 域控制器等 - 提供监视功能，以便与 Office 365 和 Microsoft Online Services 建立可靠的连接。它还可以让用户轻松访问有关这些组件的关键数据点，获取使用情况信息和其他重要见解来做出明智的决策。

这些信息显示在 [Azure AD Connect Health 门户](https://aka.ms/aadconnecthealth)中。 在 Azure AD Connect Health 门户中，可以查看警报、性能监视、使用情况分析和其他信息。 Azure AD Connect Health 在一个集中的位置提供重要标识组件的运行状况单一可重用功能区。

![什么是 Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


随着 Azure AD Connect Health 中的功能日益增多，门户通过标识可重用功能区提供单个仪表板。 可以创建一个更稳健、更健全的集成环境，让用户提高其工作效率。


## <a name="why-use-azure-ad-connect"></a>为何使用 Azure AD Connect？
将本地目录与 Azure AD 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。 用户和组织可以享受到以下好处：

* 用户可以使用单个标识来访问本地应用程序和云服务，例如 Office 365。
* 单个工具即可提供轻松同步和登录的部署体验。
* 为方案提供最新功能。 Azure AD Connect 取代了 DirSync 和 Azure AD Sync 等早期版本的标识集成工具。有关详细信息，请参阅 [混合标识目录集成工具比较](plan-hybrid-identity-design-considerations-tools-comparison.md)。

## <a name="why-use-azure-ad-connect-health"></a>为何使用 Azure AD Connect Health？
将本地目录与 Azure AD 集成可以提高用户的工作效率，因为他们可以使用一个通用标识来访问云和本地资源。 但是，这种集成带来的挑战是，必须确保此环境正常运行，才能让用户从任何设备可靠地访问本地和云中的资源。 Azure AD Connect Health 可帮助你监视和深入分析用于访问 Office 365 或其他 Azure AD 应用程序的本地标识基础结构。 这种方法就像在每个本地标识服务器上安装代理那样简单。

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[适用于 AD FS 的 Azure AD Connect Health](how-to-connect-health-adfs.md)
适用于 AD FS 的 Azure AD Connect Health 支持 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 中的 AD FS 2.0。 它还支持监视为 Extranet 访问提供身份验证支持的 AD FS 代理或 Web 应用程序代理服务器。 轻松快速地安装 Health 代理后，适用于 AD FS 的 Azure AD Connect Health 可提供以下一组重要功能：

#### <a name="key-benefits-and-best-practices"></a>主要优点和最佳做法

- *增强的安全性*
  - [Extranet 锁定趋势](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [失败的登录报告](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [符合隐私标准](reference-connect-health-user-privacy.md)    
- *[出现严重 ADFS 系统问题](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)时会收到通知*
    - 服务器配置和可用性 
    - [性能和连接](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - 定期维护    
- *易于部署和管理*
  - 快速的[代理安装](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - 代理自动升级到最新版本 
  - 数据在几分钟后便可出现在门户中    
- *丰富的[使用情况指标](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - 排名靠前的应用程序使用情况
  - 网络位置和 TCP 连接
  - 每个服务器的令牌请求    
- 出色的用户体验 
  - Azure 门户中的仪表板方式
  - [通过电子邮件提供警报](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>功能亮点

*   使用警报进行监视，以便在 AD FS 和 AD FS 代理服务器状况不正常时知道这一点
*   关键警报电子邮件通知
*   查看性能数据的趋势，这在规划 AD FS 的容量时非常有用
*   使用透视针对 AD FS 登录进行使用情况分析（应用、用户、网络位置，等等）
*   针对 AD FS 的报告，例如，使用不当的用户名/密码尝试登录的前 50 个用户和使用的最后一个 IP 地址
*   针对失败的 AD FS 登录的危险 IP 报告

在此处详细了解如何[在 AD FS 中使用 Azure AD Connect Health](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)
用于同步的 Azure AD Connect Health 可以监视和提供有关本地 Active Directory 与 Azure AD 之间发生的同步的信息。 用于同步的 Azure AD Connect Health 提供以下关键功能集：

* 使用警报进行监视，以便在 Azure AD Connect 服务器（也称为同步引擎）状况不正常时知道这一点
* 关键警报电子邮件通知
* 包括延迟图表以不同操作和趋势保持同步的同步操作见解操作如窗体添加的更新、删除
* 速览有关同步属性以及上次成功导出到 Azure AD 的信息
* 有关对象级同步错误的报告\(不需要 Azure AD Premium\)

在此处详细了解如何[使用 Azure AD Connect Health 进行同步](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[用于 AD DS 的 Azure AD Connect Health](how-to-connect-health-adds.md)
用于 Active Directory 域服务 (AD DS) 的 Azure AD Connect Health 负责监视 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 上已安装的域控制器。 使用 Health 代理安装可以从云监视本地 AD DS 环境。 用于 AD DS 的 Azure AD Connect Health 提供以下关键功能集：

* 检测到域控制器不正常时的监视警报以及关键警报的电子邮件通知
* 域控制器仪表板，提供对域控制器运行状况和运行状态的快速视图
* 复制状态仪表板，显示最新复制信息以及指向故障排除指南的链接（检测到错误时）
* 需要从任何位置快速访问常用性能计数器的性能数据关系图，才能实现故障排除和监视目的

在此处详细了解如何[在 AD DS 中使用 Azure AD Connect Health](how-to-connect-health-adds.md)

## <a name="next-steps"></a>后续步骤


- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [密码哈希同步](how-to-connect-password-hash-synchronization.md)|
- [直通身份验证](how-to-connect-pta.md)
- [Azure AD Connect 和联合身份验证](how-to-connect-fed-whatis.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)
- [版本历史记录](reference-connect-version-history.md)
- [目录集成工具比较](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect 常见问题](reference-connect-faq.md)










---
title: "Azure AD 自助服务密码重置概述 | Microsoft Docs"
description: "Azure AD 中的自助服务密码重置对组织有什么用处？"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: e05028ad46ef6ec2584cd2d3f4843cf38bb54f9e
ms.openlocfilehash: de2d41d8c2787ffdc80daddf959487f6031da87d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/16/2017

---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>面向 IT 专业人员的 Azure AD 自助服务密码重置

“自助服务”是在世界各地的众多 IT 部门中兴起的一个流行用语，其含义因情况而异。 当前市场上有大量产品都允许从云或本地管理本地组、密码或用户配置文件。

凭借着易于使用和部署，Azure Active Directory (Azure AD) 自助服务密码重置 (SSPR) 脱颖而出。 Azure AD 自助服务密码重置组合了一系列功能，它们：

* 允许用户通过以下方式管理其自己的密码
  * 从任何设备
  * 在任何位置
  * 在任何时间
* 遵循以管理员身份定义的策略

如果已准备就绪，可以使用我们的[快速入门指南](active-directory-passwords-getting-started.md)开始使用 Azure AD SSPR 并快速让用户重置其自己的密码。

## <a name="what-is-possible"></a>可以实现的功能

* **自助密码更改**允许最终用户或管理员更改其密码，不需要管理员协助
* **自助服务帐户解锁**允许最终用户解锁其自己的帐户，不需要管理员协助
* **自助服务密码重置**允许最终用户或管理员自动重置其密码，不需要管理员协助。 自助服务密码重置需要 Azure AD Premium 或 Basic - [Azure Active Directory 版本](active-directory-editions.md)。
* **管理员启动的密码重置**允许管理员通过 [Azure 门户](https://docs.microsoft.com/azure/azure-portal-overview)重置最终用户或其他管理员的密码
* **密码管理活动报告**允许管理员深入了解在其组织中发生的密码重置和注册活动 - [管理报表](active-directory-passwords-reporting.md)
* **密码写回**允许从云端管理本地密码，因此，上述所有方案都可以由联合用户或密码同步用户本人或其代表来执行。 密码写回功能需要 [Azure AD Premium](active-directory-get-started-premium.md)。

## <a name="why-choose-azure-ad-self-service-password-reset"></a>为何选择 Azure AD 自助服务密码重置

* **降低成本**，因为由支持人员协助的密码重置通常占 IT 组织 20% 的费用
* 通过允许最终用户立即解决其自己的密码问题而不需要呼叫支持人员或发出支持请求，**改进了最终用户体验**并**减小了支持人员规模**。
* **促进移动**，因为用户可以从任何位置重置其密码。

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD 自助服务密码重置可用性

Azure AD 自助服务密码重置在三个层中可用，具体取决于订阅。

* **Azure AD Free** - 仅限云的管理员可以重置其自己的密码。
* **Azure AD Basic** 或任何**付费 Office 365 订阅** - 仅限云的用户可以更改其自己的密码，仅限云的管理员可以重置其自己的密码。
* **Azure AD Premium** - 任何用户或管理员（包括仅限云、联合或密码同步的用户）都可以重置其自己的密码。 本地密码要求启用密码写回。

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Azure AD 自助服务密码重置，一系列部件的组合

Azure AD 中的自助服务密码重置由以下组件构成：

* **密码管理配置门户**，可以在其中控制有关如何通过 Azure 门户在租户中管理密码的选项
* **密码重置注册门户**，用户可以在其中自助注册密码重置
* **密码重置门户**，用户可以在其中使用由管理员定义的质询和用户已提供的答案来重置其密码
* **用户密码更改门户**，用户可以在其中通过输入旧密码并提供新密码来更改其自己的密码
* **密码管理报表**，用户可以在其中查看和分析其在 Azure 门户中的租户的密码活动报表
* **使用 Azure AD Connect 将密码写回到本地**使得可以通过云实现对本地、联合或密码同步用户的管理

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 定价、SLA、更新和路线图

可以在以下页面找到有关这些主题的更多详细信息

* [**Azure AD 定价详细信息**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 定价**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure 服务级别协议**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft Online Services 的服务级别协议**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure 更新**](https://azure.microsoft.com/updates/)
* [**Azure 路线图**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能，以及在何时何处进行访问
* [深入技术探究](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题



---
title: 将 Azure Active Directory Identity Protection 连接到 Azure 安全中心 | Microsoft Docs
description: 了解 Azure 安全中心如何与 Azure Active Directory Identity Protection 集成。
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>将 Azure Active Directory Identity Protection 连接到 Azure 安全中心
本文档可帮助配置 Azure Active Directory (AD) Identity Protection 和 Azure 安全中心之间的集成。

## <a name="why-connect-azure-ad-identity-protection"></a>为什么连接 Azure AD Identity Protection？
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可帮助检测影响组织标识的潜在漏洞。 连接后，可以在安全中心内查看 Azure AD Identity Protection 警报。 通过此集成，可以在安全中心内查看、关联和调查与混合云工作负荷相关的所有安全警报。 

## <a name="how-do-i-configure-this-integration"></a>如何配置此集成？
如果组织已在使用 Azure AD Identity Protection，请执行以下步骤来配置此集成：

1. 打开“安全中心”仪表板。
2. 在左窗格中，单击“安全解决方案”。 安全中心会自动发现是否已为组织启用了 Azure AD Identity Protection。

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. 单击“连接”。
4. 在“集成 Azure AD Identity Protection”页上向下滚动，并选择相应的工作区：

    ![工作区](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. 单击“连接”。

完成此配置后，Azure AD Identity Protection 解决方案将出现在“安全解决方案”页上的“已连接解决方案”下。 

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure AD Identity Protection 连接到安全中心。 若要详细了解安全中心，请参阅以下文章：

* [将 Microsoft Advanced Threat Analytics 连接到 Azure 安全中心](security-center-ata-integration.md)
* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [在 Azure 安全中心管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
- [Azure 安全中心的数据安全](security-center-data-security.md) - 了解如何在安全中心管理数据和确保数据安全性。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) — 获取最新的 Azure 安全新闻和信息。



---
title: "Azure 安全中心安全策略简介 | Microsoft Docs"
description: "了解 Azure 安全中心安全策略及其主要功能。"
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: f85f7077e5227818d062d114370e7344601a998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="security-policies-overview"></a>安全策略概述
本文档概述了安全中心安全策略。

## <a name="what-are-security-policies"></a>什么是安全策略？
安全策略定义了工作负载的相应配置，有助于确保用户遵守公司或法规方面的安全要求。 在安全中心内，可以定义 Azure 订阅策略，即根据工作负载类型或数据机密性量身定制。 例如，使用受管制数据（如个人身份信息）的应用程序可能需要比其他工作负载更高级别的安全性。 

安全中心策略由以下部分组成：

- 数据收集：代理预配和[数据收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)设置。
- 安全策略：用于确定安全中心监视和建议的控件的 [Azure 策略](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)（可以在安全中心内编辑，也可以使用 Azure 策略新建定义、定义其他策略和跨管理组分配策略）。
- 电子邮件通知：安全联系人和[电子邮件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)设置。
- 定价层：免费或标准[定价选择](https://docs.microsoft.com/azure/security-center/security-center-pricing)，决定了范围内的资源可以使用哪些安全中心功能（可以针对订阅、资源组和工作区进行指定）。 


## <a name="who-can-edit-security-policies"></a>谁可以编辑安全策略？
安全中心使用基于角色的访问控制 (RBAC)，提供可以分配给 Azure 中用户、组和服务的内置角色。 用户打开安全中心时，只能看到有权访问的资源的相关信息。 这意味着，可以将资源所属的订阅或资源组的“所有者”、“参与者”或“读者”角色分配给用户。 除这些角色外，还有两个特定的安全中心角色：

- 安全读者：拥有此角色的用户有权查看安全中心，包括建议、警报、策略和运行状况，但无法进行更改。
- 安全管理员：与安全读者基本相同，不同之处在于还有权更新安全策略、消除建议和警报。


## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure 安全中心安全策略。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [在 Azure 安全中心管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
- [Azure 安全中心的数据安全](security-center-data-security.md) - 了解如何在安全中心管理数据和确保数据安全性。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) — 获取最新的 Azure 安全新闻和信息。



---
title: Azure 安全中心设置 | Microsoft Docs
description: 配置 Azure 安全中心设置。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666488"
---
# <a name="security-center-settings"></a>安全中心设置
本文概述了安全中心内的设置。

可以在安全策略下访问以下设置：

- **数据收集**：确定代理预配和[数据收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)设置。
- **安全策略**：确定安全中心监视和建议的控制措施。 可在安全中心编辑[安全策略](tutorial-security-policy.md)。 还可使用 [Azure Policy](tutorial-security-policy.md) 创建新的定义、定义其他策略并跨管理组分配策略。 
- **电子邮件通知**：确定安全联系人和[电子邮件通知](security-center-provide-security-contact-details.md)设置。
- **定价层**：定义“免费”层或“标准”层的[定价选择](security-center-pricing.md)。 所选的层确定可用于范围内资源的安全中心功能。 您可以指定订阅和工作区的层。

> [!NOTE]
> 对于每个订阅，可以设置所有这些项。 对于工作区，只能设置数据收集和定价层。
>


## <a name="who-can-edit-security-policies"></a>谁可以编辑安全策略？
安全中心使用基于角色的访问控制 (RBAC)，提供可以分配给 Azure 中用户、组和服务的内置角色。 用户打开安全中心时，只能看到其有权访问的资源的相关信息。 这意味着用户将分配的角色*所有者*，*参与者*，或*读取器*资源所属的订阅。 除这些角色外，还有两个特定的安全中心角色：

- **安全读者**：有权查看安全中心，包括建议、警报、策略和运行状况，但无法进行更改。
- **安全管理员**：拥有与安全读者相同的查看权限，不同之处在于该角色有权更新安全策略、驳回建议和关闭警报。


## <a name="next-steps"></a>后续步骤
本文介绍了 Azure 安全中心安全策略。 若要了解有关 Azure 安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何为你的 Azure 订阅配置安全策略。
* [管理 Azure 安全中心的安全建议](security-center-recommendations.md)：了解安全中心建议如何帮助你保护 Azure 资源。
* [在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心数据安全性](security-center-data-security.md)：了解安全中心如何管理和保护数据。
* [Azure 安全中心常见问题解答](security-center-faq.md)：获取服务使用方面的常见问题解答。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)：获取最新的 Azure 安全性新闻和信息。

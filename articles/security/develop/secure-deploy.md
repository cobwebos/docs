---
title: 部署 Microsoft Azure 上的安全应用程序
description: 本文介绍在你的 web 应用程序项目的发布和响应阶段过程中考虑的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144446"
---
# <a name="deploy-secure-applications-on-azure"></a>部署 Azure 上的安全应用程序
在本文中，我们介绍的安全活动和控件部署的云应用程序时需要考虑。 安全问题和概念，可在 Microsoft 的发布和响应阶段过程中考虑[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)介绍。 旨在帮助你定义的活动以及可用来部署更安全的应用程序的 Azure 服务。

本文介绍以下 SDL 几个阶段：

- 发行版本
- 响应

## <a name="release"></a>发行版本
发布阶段的重点将用于公开发布的项目准备就绪。
这包括规划如何有效地执行发布后维护任务，并解决可能会出现更高版本的安全漏洞。

### <a name="check-your-applications-performance-before-you-launch"></a>检查应用程序的性能，然后启动

在启动它，或将更新部署到生产环境之前，请检查应用程序的性能。 运行基于云的[负载测试](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)通过使用 Visual Studio 应用程序中查找性能问题，提高部署质量，请确保你的应用程序始终是保持运行或可用，并且你的应用程序可以处理的流量为你启动。

### <a name="install-a-web-application-firewall"></a>安装 Web 应用程序防火墙

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 在这些攻击的影响之间常见的是 SQL 注入攻击和跨站点脚本攻击。 阻止应用程序代码中的这些攻击可能比较困难。 它可能需要严格的维护、 修补和监视应用程序拓扑的多个层面。 集中式的 WAF 可帮助简化安全管理。 WAF 解决方案还可通过修补已知的漏洞在中央位置相较保护每个单独的 web 应用程序响应安全威胁。

[Azure 应用程序网关 WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview)进行集中的保护 web 应用程序免受常见攻击和漏洞。 WAF 根据从规则[OWASP 核心规则集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)3.0 或 2.2.9。

### <a name="create-an-incident-response-plan"></a>创建事件响应计划

准备事件响应计划是非常重要，来帮助你解决可能会随着时间的推移不断涌现的新威胁。 准备事件响应计划包括识别相应的安全紧急联系人并建立安全维护服务计划从组织中其他组继承的代码和许可的第三方代码。

### <a name="conduct-a-final-security-review"></a>执行最终安全评审

谨慎地查看所执行的所有安全活动可帮助确保软件版本或应用程序准备情况。 最终安全审查 (FSR) 通常包括检查威胁模型、 工具输出和针对的质量要求和 bug 栏，在需求阶段中定义的性能。

### <a name="certify-release-and-archive"></a>认证发布和存档

对软件进行认证之前发布可帮助确保满足安全和隐私要求。 将所有相关数据存档至关重要用于执行发布后维护任务。 存档还可帮助降低持续软件工程与关联的长期成本。

## <a name="response"></a>响应
响应发布后阶段集中在开发团队能够，不可用于适当地应对新出现的软件威胁和漏洞的任何报表。

### <a name="execute-the-incident-response-plan"></a>执行事件响应计划

能够实现在版本阶段提起事件响应计划于帮助保护客户免受出现的软件安全或隐私漏洞至关重要。

### <a name="monitor-application-performance"></a>监视应用程序性能

进行持续监视应用程序的可能部署它后也可帮助你检测性能问题，以及安全漏洞。
帮助应用程序监视的 azure 服务包括：

  - Azure Application Insights
  - Azure 安全中心

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 Application Insights 会自动检测性能异常。 它提供强大的分析工具以帮助您诊断问题和了解用户实际使用你的应用。 Application Insights 有助于持续提高性能与可用性。

#### <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)可帮助你预防、 检测和响应威胁，同时增加可见性 （和控制） 的 Azure 资源，包括 web 应用程序的安全性。 Azure 安全中心可帮助检测可能会被忽略的威胁。 它适用于各种安全解决方案。

安全中心的免费层仅为 Azure 资源提供有限的安全性。 [安全中心标准层](https://docs.microsoft.com/azure/security-center/security-center-onboarding)扩展到本地资源和其他云这些功能。
安全中心标准层可帮助你：

  - 查找并修复安全漏洞。
  - 应用访问和应用程序控制来阻止恶意活动。
  - 通过使用分析和智能来检测威胁。
  - 遭到攻击时迅速响应。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议的安全控件和活动，有助于您设计和开发安全应用程序。

- [设计安全的应用程序](secure-design.md)
- [开发安全应用程序](secure-develop.md)

---
title: 在 Microsoft Azure 上部署安全应用程序
description: 本文讨论了在 Web 应用程序项目的发布和响应阶段需要考虑的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934887"
---
# <a name="deploy-secure-applications-on-azure"></a>在 Azure 上部署安全应用程序
在本文中，我们将介绍在为云部署应用程序时要考虑的安全活动和控制措施。 介绍了在 Microsoft[安全开发生命周期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的发布和响应阶段需要考虑的安全问题和概念。 目标是帮助您定义可用于部署更安全应用程序的活动和 Azure 服务。

本文将介绍以下 SDL 阶段：

- 发布
- 响应

## <a name="release"></a>发布
发布阶段的重点是为公开发布的项目做好准备。
这包括规划有效执行发布后服务任务的方法，并解决以后可能出现的安全漏洞。

### <a name="check-your-applications-performance-before-you-launch"></a>启动前检查应用程序的性能

在启动应用程序或将更新部署到生产中之前，请检查应用程序的性能。 通过使用 Visual Studio 来查找应用程序中的性能问题、提高部署质量、确保应用程序始终处于打开或可用，以及应用程序可以处理启动流量，从而运行基于云的[负载测试](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)。

### <a name="install-a-web-application-firewall"></a>安装 Web 应用程序防火墙

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 这些漏洞中常见的是 SQL 注入攻击和跨站点脚本攻击。 在应用程序代码中防止这些攻击可能具有挑战性。 它可能需要在应用程序拓扑的许多层进行严格的维护、修补和监视。 集中式 WAF 有助于简化安全管理。 WAF 解决方案还可以通过在中心位置修补已知漏洞而不是保护每个单独的 Web 应用程序来响应安全威胁。

[Azure 应用程序网关 WAF](../../application-gateway/waf-overview.md)提供 Web 应用程序的集中保护，防止常见漏洞和漏洞。 WAF 基于[OWASP 核心规则集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)3.0 或 2.2.9 中的规则。

### <a name="create-an-incident-response-plan"></a>创建事件响应计划

准备事件响应计划对于帮助您解决随时间而出现的新威胁至关重要。 准备事件响应计划包括确定适当的安全紧急联系人，并为从组织中的其他组继承的代码和许可的第三方代码建立安全服务计划。

### <a name="conduct-a-final-security-review"></a>进行最终安全审查

故意检查执行的所有安全活动有助于确保软件发布或应用程序准备就绪。 最终安全审查 （FSR） 通常包括针对需求阶段定义的质量门和 Bug 栏检查威胁模型、工具输出和性能。

### <a name="certify-release-and-archive"></a>认证发布和存档

在发布之前对软件进行认证有助于确保满足安全和隐私要求。 存档所有相关数据对于执行发布后服务任务至关重要。 归档还有助于降低与持续软件工程相关的长期成本。

## <a name="response"></a>响应
发布后响应阶段的核心是开发团队能够而且能够对任何新出现的软件威胁和漏洞的报告做出适当的响应。

### <a name="execute-the-incident-response-plan"></a>执行事件响应计划

能够实施发布阶段启动的事件响应计划对于帮助客户免受软件安全或隐私漏洞的出现至关重要。

### <a name="monitor-application-performance"></a>监视应用程序性能

部署应用程序后持续监视应用程序可能会帮助您检测性能问题以及安全漏洞。
帮助应用程序监视的 Azure 服务包括：

  - Azure Application Insights
  - Azure 安全中心

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 Application Insights 可以自动检测性能异常。 它包括强大的分析工具，可帮助您诊断问题并了解用户实际使用你的应用做什么。 Application Insights 有助于持续提高性能与可用性。

#### <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](../../security-center/security-center-intro.md)通过提高 Azure 资源（包括 Web 应用程序）的安全性，帮助您预防、检测和响应威胁。 Azure 安全中心可帮助检测可能被忽视的威胁。 它适用于各种安全解决方案。

安全中心的免费层仅为 Azure 资源提供有限的安全性。 [安全中心标准层](../../security-center/security-center-onboarding.md)将这些功能扩展到本地资源和其他云。
安全中心标准可帮助您：

  - 查找并修复安全漏洞。
  - 应用访问和应用程序控件来阻止恶意活动。
  - 使用分析和智能检测威胁。
  - 受到攻击时迅速响应。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议安全控制和活动，以帮助您设计和开发安全应用程序。

- [设计安全应用程序](secure-design.md)
- [开发安全应用程序](secure-develop.md)

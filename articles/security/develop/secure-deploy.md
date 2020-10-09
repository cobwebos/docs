---
title: 在 Microsoft Azure 上部署安全应用程序
description: 本文讨论在 Web 应用程序项目的发布和响应阶段要考虑的最佳做法。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "68934887"
---
# <a name="deploy-secure-applications-on-azure"></a>在 Azure 上部署安全应用程序
本文介绍了在为云部署应用程序时需要考虑的安全活动和控制措施。 介绍了在 Microsoft [安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 的发布和响应阶段要考虑的安全问题和概念。 目标是帮助你定义可用于部署更安全应用程序的活动和 Azure 服务。

本文涵盖以下 SDL 阶段：

- 发布
- 响应

## <a name="release"></a>发布
发布阶段的重点是准备要公开发布的项目。
这包括规划各种可有效执行发布后服务任务的方法，并解决以后可能会出现的安全漏洞。

### <a name="check-your-applications-performance-before-you-launch"></a>启动之前检查应用程序的性能

在推出应用程序或将更新部署到生产环境之前，先检查该应用程序的性能。 使用 Visual Studio 运行基于云的[负载测试](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)，以查找应用程序中的性能问题，提高部署质量，确保应用程序始终启动或可用，并且应用程序可以处理推出的流量。

### <a name="install-a-web-application-firewall"></a>安装 Web 应用程序防火墙

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 这些攻击中最常见的攻击是 SQL 注入攻击和跨站点脚本攻击。 在应用程序代码中防止这些攻击可能会很困难。 这可能需要在应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 WAF 有助于简化安全管理。 相较于保护每个单独的 Web 应用程序，WAF 解决方案还可通过在中央位置修补已知漏洞来响应安全威胁。

[Azure 应用程序网关 WAF](../../application-gateway/waf-overview.md) 可对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞的危害。 WAF 基于 [OWASP 核心规则集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9 中的规则。

### <a name="create-an-incident-response-plan"></a>创建事件响应计划

准备事件响应计划至关重要，可帮助解决随时间推移可能出现的新威胁。 准备事件响应计划包括：确定适当的安全事项紧急联系人，并为从组织中的其他组继承的代码以及获得许可的第三方代码制定安全服务计划。

### <a name="conduct-a-final-security-review"></a>进行最终安全评审

仔细检查已执行的所有安全活动有助于确保你的软件版本或应用程序准备就绪。 最终安全评审 (FSR) 通常包括针对需求阶段定义的质量检验关和 bug 栏检查威胁模型、工具输出和性能。

### <a name="certify-release-and-archive"></a>认证版本和存档

在发布之前对软件进行认证有助于确保安全和隐私要求得到满足。 存档所有相关数据对于执行发布后的服务任务至关重要。 存档还有助于降低与持续的软件工程相关的长期成本。

## <a name="response"></a>响应
发布后响应阶段的重点在于开发团队是否能够对描述所出现的软件威胁和漏洞的任何报告做出适当的响应。

### <a name="execute-the-incident-response-plan"></a>执行事件响应计划

能够实施在发布阶段制定的事件响应计划对于保护客户免受出现的软件安全或隐私漏洞的影响至关重要。

### <a name="monitor-application-performance"></a>监视应用程序性能

在部署应用程序后对应用程序进行持续监视可能有助于检测性能问题和安全漏洞。
有助于应用程序监视的 Azure 服务包括：

  - Azure Application Insights
  - Azure 安全中心

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 Application Insights 会自动检测性能异常。 其中包含功能强大的分析工具，可帮助你诊断问题并了解用户在应用中实际执行了哪些操作。 Application Insights 有助于持续提高性能与可用性。

#### <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](../../security-center/security-center-intro.md)有助于预防、检测和响应威胁，同时增加 Azure 资源（包括 Web 应用程序）在安全方面的可见性和可控性。 Azure 安全中心可帮助检测可能被忽略的威胁。 它可以与各种安全解决方案协同工作。

安全中心的免费层仅为 Azure 资源提供有限的安全性。 [安全中心标准层](../../security-center/security-center-onboarding.md)将这些功能扩展到本地资源和其他云。
安全中心标准层可帮助你：

  - 查找并修复安全漏洞。
  - 应用访问控制和应用程序控制来阻止恶意活动。
  - 使用分析和智能来检测威胁。
  - 在受到攻击时快速响应。

## <a name="next-steps"></a>后续步骤
下面的文章中推荐了一些安全控制措施和安全活动，可帮助你设计和开发安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [开发安全的应用程序](secure-develop.md)

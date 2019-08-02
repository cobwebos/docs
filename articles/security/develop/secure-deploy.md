---
title: 在 Microsoft Azure 上部署安全应用程序
description: 本文介绍了在 web 应用程序项目的发布和响应阶段要考虑的最佳实践。
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
ms.openlocfilehash: dd86e6bf571dd67da00aee63eadff031a1040ff7
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728739"
---
# <a name="deploy-secure-applications-on-azure"></a>在 Azure 上部署安全应用程序
本文介绍了在部署云应用程序时要考虑的安全活动和控制措施。 本文介绍了在 Microsoft[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的发布和响应阶段中要考虑的安全问题和概念。 其目标是帮助你定义可用于部署更安全的应用程序的活动和 Azure 服务。

本文介绍了以下 SDL 阶段:

- 发行版本
- 响应

## <a name="release"></a>发行版本
发布阶段的重点是实现公共版本的项目。
这包括用于有效执行发布后服务任务的计划方法, 并解决以后可能会出现的安全漏洞。

### <a name="check-your-applications-performance-before-you-launch"></a>启动之前检查应用程序的性能

在启动应用程序或将更新部署到生产环境之前, 请检查应用程序的性能。 使用 Visual Studio 运行基于云的[负载测试](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing), 以查找应用程序中的性能问题, 提高部署质量, 确保应用程序始终可用, 并且应用程序可以处理启动的流量。

### <a name="install-a-web-application-firewall"></a>安装 Web 应用程序防火墙

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 在这些攻击中常见的是 SQL 注入式攻击和跨站点脚本攻击。 在应用程序代码中阻止这些攻击可能会很困难。 它可能需要严格的维护、修补和监视应用程序拓扑的多个层。 集中式 WAF 有助于提高安全性管理的安全性。 WAF 解决方案还可以通过在中央位置修补已知漏洞, 而不是保护每个单独的 web 应用程序, 从而对安全威胁做出反应。

[Azure 应用程序网关 WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview)提供对 web 应用程序的集中保护, 并受到常见的攻击和漏洞的影响。 WAF 基于[OWASP 核心规则集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)3.0 或2.2.9 中的规则。

### <a name="create-an-incident-response-plan"></a>创建事件响应计划

准备事件响应计划至关重要, 可帮助解决随时间推移而出现的新威胁。 准备事件响应计划包括识别相应的安全紧急联系人, 并为从组织中的其他组继承的代码和获得许可的第三方代码建立安全服务计划。

### <a name="conduct-a-final-security-review"></a>进行最终安全审查

有意查看已执行的所有安全活动有助于确保软件版本或应用程序的准备情况。 最终安全审查 (FSR) 通常包括对照需求阶段中定义的质量入口和错误条检查威胁模型、工具输出和性能。

### <a name="certify-release-and-archive"></a>验证发布和存档

发布前验证软件有助于确保满足安全和隐私要求。 存档所有相关数据对于执行发布后服务任务至关重要。 存档还有助于降低与持续软件工程相关的长期成本。

## <a name="response"></a>响应
开发团队提供的响应后发布阶段中心能够正确响应任何新兴软件威胁和漏洞的报告。

### <a name="execute-the-incident-response-plan"></a>执行事件响应计划

能够实现在 "发布" 阶段中开始的事件响应计划对于帮助客户避免出现的软件安全或隐私漏洞非常重要。

### <a name="monitor-application-performance"></a>监视应用程序性能

在部署应用程序后对应用程序进行的持续监视可能会帮助你检测性能问题和安全漏洞。
有助于应用程序监视的 Azure 服务包括:

  - Azure Application Insights
  - Azure 安全中心

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 Application Insights 会自动检测性能异常。 它包含功能强大的分析工具, 可帮助你诊断问题并了解用户对你的应用程序的实际操作。 Application Insights 有助于持续提高性能与可用性。

#### <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)可帮助预防、检测和响应威胁, 同时提高 Azure 资源 (包括 web 应用程序) 的安全性。 Azure 安全中心可帮助检测可能被忽略的威胁。 它与各种安全解决方案一起工作。

安全中心的免费层仅为 Azure 资源提供有限的安全性。 [安全中心标准层](https://docs.microsoft.com/azure/security-center/security-center-onboarding)将这些功能扩展到本地资源和其他云。
安全中心标准可帮助你:

  - 查找并修复安全漏洞。
  - 应用访问和应用程序控制来阻止恶意活动。
  - 使用分析和智能检测威胁。
  - 在受到攻击时快速响应。

## <a name="next-steps"></a>后续步骤
在下面的文章中, 我们建议可以帮助你设计和开发安全应用程序的安全控制和活动。

- [设计安全的应用程序](secure-design.md)
- [开发安全的应用程序](secure-develop.md)

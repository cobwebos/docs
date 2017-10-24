---
title: "Azure 安全中心简介 | Microsoft 文档"
description: "了解 Azure 安全中心及其主要功能和工作原理。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 21415af0d449d639d000e07afdb4de3680a64774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-security-center"></a>Azure 安全中心简介
了解 Azure 安全中心及其主要功能和工作原理。

## <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。  借助它，不仅可以监视和控制混合云工作负载，还能积极防御以减少面临的威胁，并智能检测以从容应对瞬息万变的网络攻击。

在“安全中心概述”中，可快速概览 Azure 和非 Azure 工作负载的安全状态，有助于发现并评估工作负载的安全性，同时发现和缓解风险。

![概述](./media/security-center-intro/security-center-intro-fig1.png)

## <a name="why-use-security-center"></a>为什么要使用安全中心？

**统一监视和控制**

- **跨混合工作负载了解安全状态**。 在一个控制台中集中管理所有混合云工作负载（本地、Azure 和其他云平台）的安全性。 可通过内置仪表板快速了解需要注意的安全问题。
- **监视云工作负载**。 适应瞬息万变的云工作负载。 自动发现并载入 Azure 订阅中新建的资源。
- **集中式策略管理**。 通过集中管理所有混合云工作负载中的安全策略确保符合公司或法规安全要求。
- **来自多个源的安全数据**。 收集、搜索并分析来自各种来源（包括网络防火墙等已连接的合作伙伴解决方案以及其他 Microsoft 服务）的安全数据。 
- **与现有安全工作流集成**。 使用 REST API 连接已有工具和进程，从而访问、集成和分析安全信息。
- **符合性报告**。 使用安全数据和见解展示符合性，并为审核员轻松生成证据。

**自适应威胁防御**

- **持续进行安全评估**。 使用数百个内置安全评估或创建自己的安全评估，监控计算机、网络和 Azure 服务的安全性。 识别出易受攻击的软件和配置。
- **切实可行的建议**。 借助高优先级且可行的安全建议和内置自动操作手册，在攻击者利用安全漏洞前修复这些漏洞。
- **自适应应用程序控件**。 通过应用适合特定 Azure 工作负载且由机器学习提供支持的白名单建议，阻止恶意软件和其他不需要的应用程序。 
- **网络访问安全**。 通过对 Azure VM 上管理端口的实时控制访问减小网络攻击面，显著减小在暴力和其他网络攻击下的曝光面。

**智能威胁检测和响应**

- **业界最全面的威胁智能工具**。 利用 Microsoft Intelligent Security Graph，通过全球各地的 Microsoft 服务和系统发送的数万亿信号识别新的及不断演变的威胁。
- **高级威胁检测**。 使用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。
- **设置警报和事件的优先级**。 借助高优先级的安全警报和事件首先关注将各种类型的警报映射到单个攻击活动的最紧急的威胁。 也可创建自己的自定义安全警报。
- **简化的调查体验**。 借助交互式的可视体验，可快速评估攻击的范围和影响。 使用预定义或即席查询深入探索安全数据。 
- **上下文式威胁智能**。 在交互式世界地图上将攻击源可视化。 使用内置威胁智能报表获取有关已知恶意执行组件的技术和目标的有价值的见解。

## <a name="get-started"></a>入门
若要开始使用安全中心，需要具有 Microsoft Azure 订阅。 安全中心通过 Azure 订阅启用。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 

[Azure 安全中心入门](https://docs.microsoft.com/azure/security-center/security-center-get-started)提供有安全中心的安全监视和策略管理组件的快速指南。 


## <a name="next-steps"></a>后续步骤
本文档介绍了安全中心以及其主要功能和如何入门。 若要了解更多信息，请参阅下列资源：

* [Azure 安全中心计划和操作指南](security-center-planning-and-operations-guide.md) - 了解如何根据组织的安全要求和云管理模型，优化安全中心使用。
* [设置安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies) - 了解如何为 Azure 订阅和资源组配置安全策略。
* [管理安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations) - 了解安全建议如何有助于保护 Azure 和非 Azure 资源。
* [安全运行状况监视](https://docs.microsoft.com/azure/security-center/security-center-monitoring) - 了解如何监视 Azure 和非 Azure 资源的运行状况。
* [管理和应对安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - 了解如何管理和应对安全警报。
* [监视合作伙伴解决方案](https://docs.microsoft.com/azure/security-center/security-center-partner-solutions) - 了解如何监视合作伙伴解决方案的运行状况。
* [安全中心 FAQ](https://docs.microsoft.com/azure/security-center/security-center-faq) - 查找有关如何使用安全中心的常见问题解答。



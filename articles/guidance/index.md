---
title: "缓存指南 | Microsoft 文档"
description: "Azure 最佳实践和指南"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Azure 指南
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsoft 模式和实践团队是 Azure 客户顾问团队的一部分。 我们的目的是帮助开发人员、架构师和 IT 专业人员在 Microsoft Azure Platform 上取得成功。 我们开发了指南，用于说明在 Azure 上构建云解决方案的最佳实践。

## <a name="checklists"></a>清单
这些列表是用于检查可用性和可伸缩性的基本情况的快速参考。 

* [可用性核对清单][AvailabilityChecklist] 
  
    确保可用性的建议做法摘要。
* [可伸缩性清单][ScalabilityChecklist]
  
    设计和实现可缩放服务以及处理数据管理的建议做法摘要。

## <a name="best-practices-articles"></a>最佳实践文章
这些文章深入讨论了通常与云计算关联的重要概念。 

* [API 设计][APIDesign] 
  
    讨论在设计 Web API 时要考虑的设计问题。
* [API 实现][APIImplementation] 
  
    一套用于实现和发布 Web API 的建议做法。
* [Azure 安全指南](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    讨论身份验证和授权问题（例如，令牌类型、授权协议、授权流程和威胁缓解）。
* [自动缩放指南][AutoscalingGuidance] 
  
    有关无需手动干预即可执行缩放的解决方案注意事项摘要。
* [后台作业指南][BackgroundJobsGuidance] 
  
    介绍用于实施应在后台（独立于任何前台或交互式操作）执行的任务的可用选项和建议做法。
* [内容交付网络 (CDN) 指南][CDNGuidance] 
  
    有关使用 CDN 最大程度地降低应用程序负载并最大程度地提高可用性和性能的常规指南和建议做法。
* [缓存指南][CachingGuidance] 
  
    有关如何使用缓存提高系统性能和可伸缩性的摘要。
* [数据分区指南][DataPartitioningGuidance]
  
    可用于对数据进行分区以改善可伸缩性、减少争用以及优化性能的策略。
* [监视和诊断指南][MonitoringandDiagnosticsGuidance] 
  
    有关跟踪用户利用你系统的方式、跟踪资源利用率以及通常监视系统的运行状况和性能的指南。
* [建议的命名约定][naming-conventions] 
  
    Azure 资源的建议命名约定。
* [有关重试的一般性指南][RetryGeneralGuidance] 
  
    讨论有关处理暂时性故障的一般概念。
* [重试服务指南][RetryServiceSpecificGuidance]
  
    用于许多 Azure 服务的重试功能的摘要，包含帮助使用、适应或扩展相应服务的重试机制的信息。

## <a name="scenario-guides"></a>方案指南
* [在 Azure 上运行 Elasticsearch][elasticsearch] 
  
    Elasticsearch 是高度可缩放的开源搜索引擎和数据库。 它适用于需要快速分析和发现大型数据集中所保留信息的情况。 本指南介绍了在设计 Elasticsearch 群集时要考虑的一些重要方面。
* [多租户应用程序的标识管理][identity-multitenant] 
  
    “多租户”是一种体系结构，其中多个租户共享某个应用程序，但彼此之间相互独立。 本指南说明如何使用 [Azure Active Directory][AzureAD] 处理登录和身份验证，从而在多租户应用程序中管理用户标识。
* [开发大数据解决方案](https://msdn.microsoft.com/library/dn749874.aspx)
  
    本指南介绍如何将 HDInsight 用于一些应用场景，例如作为数据仓库用于迭代浏览、用于 ETL 进程以及用于集成到现有 BI 系统。 它还包括有关了解大数据的概念、规划和设计大数据解决方案以及实施这些解决方案的指南。

## <a name="patterns"></a>模式
* [云设计模式：云应用程序的的规范性体系结构指南](https://msdn.microsoft.com/library/dn568099.aspx)
  
    云设计模式是设计模式和相关指南主题的库。 它通过说明每个部分可以如何放入云应用程序体系结构中来阐述应用模式的好处。
* [优化云应用程序的性能](https://github.com/mspnp/performance-optimization)
  
    本指南介绍了妨碍应用在有负载时缩放的常见反模式。 它包括演示八个反模式的示例、 [性能分析入门](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md)和有关[评估针对关键指标的性能](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md)的指南。

## <a name="reference-architectures"></a>参考体系结构
我们的参考体系结构是按应用场景安排的。
每个体系结构都提供建议做法和规范的步骤以及体现建议的可执行组件。

可在 [http://aka.ms/architecture](http://aka.ms/architecture) 上找到当前的参考体系结构库。

## <a name="resiliency-guidance"></a>复原指南
这些主题描述如何设计在分布式云环境中发生故障时可复原的应用程序。   

* [复原概述][ResiliencyOvervew]
  
     如何在 Azure 平台上构建可从故障中恢复并继续工作的应用程序。 介绍实现复原（从设计到实施、部署和操作）的结构化方法。
* [复原清单][resiliency-checklist]
  
    帮助针对可能会发生的各种故障模式制定计划的建议清单。
* [故障模式分析][resiliency-fma] 
  
    故障模式分析 (FMA) 是个过程，用于通过标识可能的故障点将复原能力内置到系统中。 作为 FMA 过程的起点，这篇文章包含了潜在故障模式及其缓解措施的目录。 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Jan17_HO3-->



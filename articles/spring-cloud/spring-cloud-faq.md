---
title: Azure 春季 Cloud 常见问题 |Microsoft Docs
description: 查看 Azure 春季 Cloud 常见问题解答
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 1fb241ab1335e6b9c9be55acbbe2e21378831b69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607231"
---
# <a name="frequently-asked-questions"></a>常见问题

本文解决了有关 Azure 春季云的常见问题。 

## <a name="general"></a>常规

### <a name="why-azure-spring-cloud"></a>为什么选择 Azure 春季云？

Azure 春季云为春季开发人员提供了平台即服务（PaaS）。 Azure 春季云管理你的应用程序基础结构，因此你可以专注于应用程序代码和业务逻辑。 内置于 Azure 春季云中的核心功能包括 Eureka、Config Server、Service Registry Server、Pivotal 生成服务、蓝绿色部署等。 此服务还允许开发人员将其应用程序与 Azure 服务（例如 CosmosDB、MySQL 和用于 Redis 的 Azure 缓存）绑定在一起。

Azure 春季云通过集成 Azure Monitor、Application Insights 和 Log Analytics 增强了开发人员和运营商的应用程序诊断体验。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure 春季云提供哪些服务计划？

在预览期间，Azure 春季云提供一个服务计划。  春季云部署包含16个 vCPU 核心和32GB 内存。  部署中每个微服务实例的上限为 4 vCPU 核，内存为8GB。

资源 | 金额
------- | -------
每个弹簧应用程序的应用实例 | 20
每个 Azure 春季云服务实例的应用实例总数 | 50 *
每个订阅每个区域的 Azure 春季云服务实例 | pps-2
永久性卷 | 10 x 50 Gb

*_打开[支持票证](https://azure.microsoft.com/support/faq/)以提高此限制。_

有关更多详细信息，请参阅[Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure 春季云的安全性如何？

安全和隐私是 Azure 和 Azure 春季云客户的头等大事之一。 Azure 通过安全加密所有此数据，确保只有客户可以访问应用程序数据、日志或配置。 Azure 春季云中的所有服务实例彼此隔离。

Azure 春季云提供完整的 SSL 和证书管理。

适用于 OpenJDK 和春季云运行时的关键安全修补程序尽快应用于 Azure 春季 Cloud。

### <a name="which-regions-azure-spring-cloud-are-available"></a>Azure 春季云和哪些区域可用？

美国东部、美国西部2、西欧和东南亚。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季云的已知限制是什么

下面是 Azure 春季云的已知限制，而服务处于预览阶段。

* `spring.application.name` 将被用于创建每个应用程序的应用程序名称替代。
* Git 存储库中的配置文件中不允许 `server.port`。 将其添加到配置文件可能会导致应用程序无法从其他应用程序或 internet 访问。
* Azure 门户和资源管理器模板不支持上传应用程序包。 这只能通过 Azure CLI 的应用程序部署来完成。
* 有关配额限制，请参阅[Azure 春季云产品/服务计划](#what-service-plans-does-azure-spring-cloud-offer)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供反馈和报告问题？

如果在 Azure 春季云中遇到任何问题，请创建[Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 有关功能请求，请参阅[Azure 反馈](https://feedback.azure.com/forums/34192--general-feedback)以请求功能或提供反馈。

## <a name="development"></a>开发

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>我是一名开发人员，但是 Azure 的新手，最快的方法是了解如何开发 Azure 春季云应用程序？

Azure 春季 Cloud 入门最快的方法是遵循[此快速入门](spring-cloud-quickstart-launch-app-portal.md)。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>什么 Java 运行时 Azure 春季云支持？

Azure 春季云支持 Java 8 和11。

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>我在哪里可以看到我的春季应用程序日志和指标？

在 "应用概述" 选项卡和 " [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) " 选项卡中查找指标。

Azure 春季云支持将春季应用程序日志和指标导出到 Azure 存储、EventHub 和[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的表名称 `AppPlatformLogsforSpring`。 若要启用它，请查看有关我们的[诊断服务](diagnostic-services.md)的这篇文章。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure 春季云是否支持分布式跟踪？

是的，请访问[分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)了解更多详细信息。

### <a name="what-resource-types-does-service-binding-support"></a>服务绑定支持哪些资源类型？

目前支持三种服务： Azure Cosmos DB、Azure Database for MySQL 和适用于 Redis 的 Azure 缓存。

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>我是否可以从我的应用程序内部查看/添加或移动永久性卷？
可以。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季云是否支持蓝绿色部署？
是的，请访问[过渡环境指南](spring-cloud-howto-staging-environment.md)了解更多详细信息。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>是否可以访问 Kubernetes 来操作应用程序容器？

不能。  Azure 春季云使开发人员从底层体系结构中抽象化，使你能够专注于应用程序代码和业务逻辑。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure 春季云是否支持从源构建容器？

是的，请访问[从源部署](spring-cloud-launch-from-source.md)以了解更多详细信息。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure 春季 Cloud 是否支持应用实例中的自动缩放？

不能。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>将现有春季微服务迁移到 Azure 春季云的最佳做法是什么？

将现有弹簧微服务迁移到 Azure 春季云之前，
* 需要解析所有应用程序依赖项。
* 准备好配置条目、环境变量和 JVM 参数，以便将其与 Azure 春季云中的部署进行比较。
* 如果要使用服务绑定，请访问 Azure 服务并确保已设置适当的访问权限。
* 建议删除或禁用嵌入的服务，这些服务可能与由 Azure 春季 Cloud 管理的服务（如我们的服务发现服务、配置服务器等）冲突。
*-* 建议使用官方和稳定的 Pivotal 弹簧库。 Pivotal 弹簧库的非正式版本、beta 版本或分叉版本不提供 SLA 支持。

迁移后，监视 CPU/RAM 指标和网络流量，以确保适当地缩放应用程序实例。

## <a name="next-steps"></a>后续步骤

[如果有更多问题，请查看故障排除指南](spring-cloud-troubleshoot.md)。
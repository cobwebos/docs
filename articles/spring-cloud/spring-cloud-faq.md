---
title: Azure 春季 Cloud 常见问题 |Microsoft Docs
description: 本文解答了有关 Azure 春季云的常见问题。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: e4fbeef06ae49ffe24f84b1a12dbcdfe0a5f1fec
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278570"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春季云常见问题解答

本文解答了有关 Azure 春季云的常见问题。 

## <a name="general"></a>常规

### <a name="why-azure-spring-cloud"></a>为何要使用 Azure Spring Cloud？

Azure 春季云为春季云开发人员提供了平台即服务（PaaS）。 Azure 春季云管理你的应用程序基础结构，以便你可以专注于应用程序代码和业务逻辑。 内置于 Azure 春季云中的核心功能包括 Eureka、Config Server、Service Registry Server、Pivotal 生成服务、蓝绿色部署等。 此服务还允许开发人员将其应用程序与其他 Azure 服务（例如 Azure Cosmos DB、Azure Database for MySQL 和 Redis 的 Azure 缓存）绑定。

Azure 春季云通过集成 Azure Monitor、Application Insights 和 Log Analytics 增强了开发人员和操作员的应用程序诊断体验。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud 提供哪些服务计划？

Azure Spring Cloud 在预览期提供一个服务计划。  春季云部署包含16个 vCPU 核心和 32 gb 的内存。  部署中每个微服务实例的上限为4个具有 8 GB 内存的 vCPU 核心。

资源 | 总量
------- | -------
每个 Spring 应用程序的应用实例数 | 20
每个 Azure Spring Cloud 服务实例的应用实例总数 | 500
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 10
永久性卷 | 10 x 50 GB

\*_提高限制，请打开[支持票证](https://azure.microsoft.com/support/faq/)。_

有关详细信息，请参阅[Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全和隐私是 Azure 和 Azure 春季云客户的头等大事。 Azure 通过安全加密所有这些数据，帮助确保只有客户可以访问应用程序数据、日志或配置。 Azure Spring Cloud 中的所有服务实例彼此隔离。

Azure Spring Cloud 提供完整的 SSL 和证书管理。

适用于 OpenJDK 和 Spring Cloud 运行时的关键安全修补程序会尽快应用到 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 春季云在哪些区域可用？

美国东部、美国西部 2、西欧和东南亚。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季云的已知限制是什么？

在预览版期间，Azure 春季云具有以下已知限制：

* `spring.application.name` 将被用于创建每个应用程序的应用程序名称覆盖。
* Git 存储库的配置文件中不允许 `server.port`。 将其添加到配置文件可能会导致应用程序无法从其他应用程序或 internet 中访问。
* Azure 门户和 Azure 资源管理器模板不支持上传应用程序包。 仅可通过 Azure CLI 部署应用程序来上载应用程序包。
* 若要了解配额限制，请参阅[Azure 春季云产品/服务的服务计划是什么？](#what-service-plans-does-azure-spring-cloud-offer)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供反馈和报告问题？

如果在 Azure 春季云中遇到任何问题，请创建[Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 若要提交功能请求或提供反馈，请参阅[Azure 反馈](https://feedback.azure.com/forums/34192--general-feedback)。

## <a name="development"></a>开发

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是一名云开发人员，而是 Azure 的新手。 了解如何开发 Azure 春季云应用程序的最快方法是什么？

若要快速开始使用 Azure 春季云，请按照[快速入门：使用 Azure 门户启动 Azure 春季 cloud 应用程序](spring-cloud-quickstart-launch-app-portal.md)中的说明进行操作。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支持哪些 Java 运行时？

Azure Spring Cloud 支持 Java 8 和 11。

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>在哪里可以查看春季 Cloud 应用程序日志和指标？

在“应用概述”选项卡和“[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)”选项卡中可以找到指标。

Azure 春季云支持将春季 Cloud 应用程序日志和指标导出到 Azure 存储、EventHub 和[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的表名为*AppPlatformLogsforSpring*。 若要了解如何启用它，请参阅[诊断服务](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支持分布式跟踪？

可以。 有关详细信息，请参阅[教程：在 Azure 春季云中使用分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服务绑定支持哪些资源类型？

目前支持三种服务：用于 Redis 的 Azure Cosmos DB、Azure Database for MySQL 和 Azure Cache。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>能否在我的应用程序中查看、添加或移动永久性卷？

可以。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季云是否支持蓝绿色部署？
可以。 有关详细信息，请参阅[设置过渡环境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>是否可以访问 Kubernetes 来操作应用程序容器？

不。  Azure Spring Cloud 可使开发人员抽身于底层体系结构，将注意力放在应用程序代码和业务逻辑上。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支持从源构建容器？

可以。 有关详细信息，请参阅[从源代码启动春季云应用程序](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支持应用实例中的自动缩放？

不。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>将现有春季 Cloud 微服务迁移到 Azure 春季云的最佳做法是什么？

在将现有春季 Cloud 微服务迁移到 Azure 春季云时，最好遵循以下最佳做法：
* 需要解析所有应用程序依赖项。
* 准备好配置条目、环境变量和 JVM 参数，以便可以将它们与 Azure 春季云中的部署进行比较。
* 如果要使用服务绑定，请访问 Azure 服务并确保已设置适当的访问权限。
* 建议删除或禁用任何可能与 Azure 春季云管理的服务冲突的嵌入服务，例如我们的服务发现服务、配置服务器等。
* 建议使用正式的、稳定的 Pivotal 弹簧库。 Pivotal 弹簧库的非正式版本、beta 版本或分叉版本没有服务级别协议（SLA）支持。

迁移后，监视 CPU/RAM 指标和网络流量，以确保适当地缩放应用程序实例。

## <a name="next-steps"></a>后续步骤

如果你有其他问题，请参阅[Azure 春季云故障排除指南](spring-cloud-troubleshoot.md)。

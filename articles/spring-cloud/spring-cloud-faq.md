---
title: 有关 Azure Spring Cloud 的常见问题解答 | Microsoft 文档
description: 查看有关 Azure Spring Cloud 的常见问题解答
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 09970468a277dcaf3f28b4f5065572568089a12e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038486"
---
# <a name="frequently-asked-questions"></a>常见问题

本文解答有关 Azure Spring Cloud 的常见问题。 

## <a name="general"></a>常规

### <a name="why-azure-spring-cloud"></a>为何要使用 Azure Spring Cloud？

Azure Spring Cloud 为 Spring 开发人员提供平台即服务 (PaaS)。 Azure Spring Cloud 可以管理应用程序基础结构，使你能够专注于应用程序代码和业务逻辑。 Azure Spring Cloud 中内置的核心功能包括 Eureka、配置服务器、服务注册表服务器、Pivotal Build Service、蓝绿部署，等等。 此服务还可让开发人员将其应用程序绑定到 CosmosDB、MySQL 和 Azure Redis 缓存等 Azure 服务。

Azure Spring Cloud 通过集成 Azure Monitor、Application Insights 和 Log Analytics 来增强开发人员和运营商的应用程序诊断体验。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud 提供哪些服务计划？

Azure Spring Cloud 在预览期是免费的。

资源 | 金额
------- | -------
vCPU | 4
内存 | 8 GB
每个 Spring 应用程序的应用实例数 | 20
每个 Azure Spring Cloud 服务实例的应用实例总数 | 50*
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 2*
永久性卷 | 10 x 50 GB

*可以开具[支持票证](https://azure.microsoft.com/support/faq/)来提高限制。 

有关更多详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隐私性是 Azure 及 Azure Spring Cloud 客户最关注的问题之一。 Azure 将会安全加密所有应用程序数据、日志或配置，确保只有各自的客户才能访问这些数据。 Azure Spring Cloud 中的所有服务实例彼此隔离。

Azure Spring Cloud 提供完整的 SSL 和证书管理。

适用于 OpenJDK 和 Spring Cloud 运行时的关键安全修补程序会尽快应用到 Azure Spring Cloud。

### <a name="which-regions-azure-spring-cloud-are-available"></a>哪些区域推出了 Azure Spring Cloud？

美国东部、美国西部 2、西欧和东南亚。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud 有哪些已知限制？

预览版的 Azure Spring Cloud 服务存在以下已知限制。

* 用于创建每个应用程序的应用程序名称将会覆盖 `spring.application.name`。
* 来自 Git 存储库的配置文件中不允许 `server.port`。 将它添加到配置文件有可能会导致无法从其他应用程序或 Internet 访问你的应用程序。
* Azure 门户和资源管理器模板不支持上传应用程序包。 只能使用 Azure CLI 通过应用程序部署执行此操作。
* 有关配额限制，请参阅 [Azure Spring Cloud 提供哪些服务计划](#what-service-plans-does-azure-spring-cloud-offer)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供反馈和报告问题？

如果你在 Azure Spring Cloud 中创建了 Spring 服务实例，则可以创建 [Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 如果你尚未登记到 Azure Spring Cloud，可以通过 [Azure 反馈](https://feedback.azure.com/)来请求功能或提供反馈。

## <a name="development"></a>开发

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>我是一名 Spring 开发人员，但不太熟悉 Azure，了解如何开发 Azure Spring Cloud 应用程序的捷径是什么？

Azure Spring Cloud 入门捷径是遵循[此快速入门](spring-cloud-quickstart-launch-app-portal.md)。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支持哪些 Java 运行时？

Azure Spring Cloud 支持 Java 8 和 11。

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>在哪里可以查看 Spring 应用程序日志和指标？

在“应用概述”选项卡和“[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)”选项卡中可以找到指标。

Azure Spring Cloud 支持将 Spring 应用程序日志和指标导出到 Azure 存储、事件中心和 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的表名为 `AppPlatformLogsforSpring`。 若要启用此功能，请查看有关[诊断服务](diagnostic-services.md)的文章。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支持分布式跟踪？

是的。有关更多详细信息，请访问[分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服务绑定支持哪些资源类型？

目前支持三个服务：Azure Cosmos DB、Azure Database for MySQL 和 Azure Redis 缓存。

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>是否可以从应用程序内部查看/添加/移动永久性卷？
是的。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud 是否支持蓝绿部署？
是的。有关更多详细信息，请访问[过渡环境指南](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>是否可以访问 Kubernetes 来操作应用程序容器？

不是。  Azure Spring Cloud 可使开发人员抽身于底层体系结构，将注意力放在应用程序代码和业务逻辑上。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支持从源构建容器？

是的。有关更多详细信息，请访问[从源部署](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支持应用实例中的自动缩放？

不是。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>将现有 Spring 微服务迁移到 Azure Spring Cloud 的最佳做法是什么？

将现有 Spring 微服务迁移到 Azure Spring Cloud 之前：
* 需要解析所有应用程序依赖项。
* 准备好配置项、环境变量和 JVM 参数，以便可将其与 Azure Spring Cloud 中的部署进行比较。
* 若要使用服务绑定，请分析你的 Azure 服务，并确保设置适当的访问权限。
* 我们建议删除或禁用可能与 Azure Spring Cloud 管理的服务（例如，我们的服务发现服务、配置服务器等）相冲突的嵌入式服务。
*-* 我们建议使用稳定的 Pivotal Spring 正式库。 非正式版、beta 版或克隆版的 Pivotal Spring 库不附带 SLA 支持。

迁移后，请监视 CPU/RAM 指标和网络流量，以确保适当缩放应用程序实例。

## <a name="next-steps"></a>后续步骤

[如有其他问题，请查看故障排除指南](spring-cloud-troubleshoot.md)。
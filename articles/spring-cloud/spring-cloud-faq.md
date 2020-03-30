---
title: 有关 Azure 春云的常见问题 |微软文档
description: 本文回答了有关 Azure 春云的常见问题。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298761"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春云常见问题解答

本文回答了有关 Azure 春云的常见问题。 

## <a name="general"></a>常规

### <a name="why-azure-spring-cloud"></a>为何要使用 Azure Spring Cloud？

Azure 春云为春云开发人员提供了一个平台即服务 （PaaS）。 Azure Spring Cloud 管理应用程序基础结构，以便您可以专注于应用程序代码和业务逻辑。 Azure 春云中内置的核心功能包括尤里卡、配置服务器、服务注册表服务器、Pivotal 构建服务、蓝绿色部署等。 此服务还使开发人员能够将其应用程序与其他 Azure 服务绑定，例如 Azure 宇宙数据库、MySQL 的 Azure 数据库和 Redis 的 Azure 缓存。

Azure Spring Cloud 通过集成 Azure 监视器、应用程序见解和日志分析，增强了开发人员和操作员的应用程序诊断体验。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud 提供哪些服务计划？

Azure Spring Cloud 在预览期提供一个服务计划。  Spring Cloud 部署包含 16 个 vCPU 内核和 32 GB 内存。  部署中的每个微服务实例的上限为 4 个 vCPU 内核，内存为 8 GB。

资源 | 金额
------- | -------
每个 Spring 应用程序的应用实例数 | 20
每个 Azure Spring Cloud 服务实例的应用实例总数 | 500
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 10
永久性卷 | 10 x 50 GB

\*_要提高限制，打开[支持票证](https://azure.microsoft.com/support/faq/)。_

有关详细信息，请参阅[Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隐私是 Azure 和 Azure 春云客户的首要任务之一。 Azure 通过安全地加密所有这些数据，有助于确保只有客户才能访问应用程序数据、日志或配置。 Azure Spring Cloud 中的所有服务实例彼此隔离。

Azure 春云提供完整的 TLS/SSL 和证书管理。

适用于 OpenJDK 和 Spring Cloud 运行时的关键安全修补程序会尽快应用到 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 春云在哪些区域可用？

美国东部、美国西部 2、西欧和东南亚。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春云的已知限制是什么？

在预览发布期间，Azure 春云具有以下已知限制：

* `spring.application.name`将覆盖用于创建每个应用程序的应用程序名称。
* `server.port`不允许在 Git 存储库中的配置文件中。 将其添加到配置文件可能会使应用程序无法从其他应用程序或 Internet 访问。
* Azure 门户和 Azure 资源管理器模板不支持上载应用程序包。 只能通过 Azure CLI 部署应用程序来上载应用程序包。
* 要了解配额限制，请参阅[Azure 春云提供哪些服务计划？](#what-service-plans-does-azure-spring-cloud-offer)

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供反馈和报告问题？

如果 Azure 春云遇到任何问题，请创建[Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 要提交功能请求或提供反馈，请转到[Azure 反馈](https://feedback.azure.com/forums/34192--general-feedback)。

## <a name="development"></a>开发

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是春云开发人员，但属于 Azure。 我学习如何开发 Azure 春云应用程序的最快方法是什么？

要获得使用 Azure 春云的最快方法，请按照["快速入门：使用 Azure 门户启动 Azure 春云"应用程序](spring-cloud-quickstart-launch-app-portal.md)。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支持哪些 Java 运行时？

Azure Spring Cloud 支持 Java 8 和 11。

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>在哪里可以查看 Spring Cloud 应用程序日志和指标？

在“应用概述”选项卡和“[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)”选项卡中可以找到指标。

Azure Spring Cloud 支持将春云应用程序日志和指标导出到 Azure 存储、事件Hub 和[日志分析](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 日志分析中的表名称是*AppPlatformLogsforSpring*。 要了解如何启用它，请参阅[诊断服务](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支持分布式跟踪？

是的。 有关详细信息，请参阅[教程：使用 Azure 春云进行分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服务绑定支持哪些资源类型？

当前支持三个服务：Azure 宇宙数据库、MySQL 的 Azure 数据库和用于 Redis 的 Azure 缓存。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>是否可以查看、添加或移动应用程序内部的持久卷？

是的。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春云是否支持蓝绿色部署？
是的。 有关详细信息，请参阅[设置暂存环境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>是否可以访问 Kubernetes 来操作应用程序容器？

不是。  Azure Spring Cloud 可使开发人员抽身于底层体系结构，将注意力放在应用程序代码和业务逻辑上。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支持从源构建容器？

是的。 有关详细信息，请参阅[从源代码启动春云应用程序](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支持应用实例中的自动缩放？

不是。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>将现有春云微服务迁移到 Azure 春云的最佳做法是什么？

将现有的 Spring Cloud 微服务迁移到 Azure 春云时，最好遵守以下最佳实践：
* 需要解析所有应用程序依赖项。
* 准备配置条目、环境变量和 JVM 参数，以便将它们与 Azure Spring Cloud 中的部署进行比较。
* 如果要使用服务绑定，请浏览 Azure 服务并确保已设置适当的访问权限。
* 我们建议您删除或禁用可能与 Azure Spring Cloud 管理的服务（如我们的服务发现服务、配置服务器等）冲突的任何嵌入式服务。
* 我们建议您使用官方、稳定的枢轴泉库。 Pivotal Spring 库的非官方、测试版或分叉版本没有服务级别协议 （SLA） 支持。

迁移后，监视 CPU/RAM 指标和网络流量，以确保应用程序实例得到适当缩放。

## <a name="next-steps"></a>后续步骤

如果另有问题，请参阅 Azure[春云故障排除指南](spring-cloud-troubleshoot.md)。

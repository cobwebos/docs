---
title: Azure 春季 Cloud 常见问题 |Microsoft Docs
description: 本文解答了有关 Azure 春季云的常见问题。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 9cdbe0630060c9155187e1f62aba3d7a40ceae98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610466"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春季云常见问题解答

本文解答了有关 Azure 春季云的常见问题。

## <a name="general"></a>常规

### <a name="why-azure-spring-cloud"></a>为何要使用 Azure Spring Cloud？

Azure 春季云为春季云开发人员提供了平台即服务（PaaS）。 Azure 春季云管理你的应用程序基础结构，以便你可以专注于应用程序代码和业务逻辑。 内置于 Azure 春季云中的核心功能包括 Eureka、Config Server、Service Registry Server、Pivotal 生成服务、蓝绿色部署等。 此服务还允许开发人员将其应用程序与其他 Azure 服务（例如 Azure Cosmos DB、Azure Database for MySQL 和 Redis 的 Azure 缓存）绑定。

Azure 春季云通过集成 Azure Monitor、Application Insights 和 Log Analytics 增强了开发人员和操作员的应用程序诊断体验。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全和隐私是 Azure 和 Azure 春季云客户的头等大事。 Azure 通过安全加密所有这些数据，帮助确保只有客户可以访问应用程序数据、日志或配置。 

* Azure 春季云中的服务实例彼此隔离。
* Azure 春季云提供完整的 TLS/SSL 和证书管理。
* 适用于 OpenJDK 和 Spring Cloud 运行时的关键安全修补程序会尽快应用到 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 春季云在哪些区域可用？

美国东部、美国西部 2、西欧和东南亚。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季云的已知限制是什么？

在预览版期间，Azure 春季云具有以下已知限制：

* `spring.application.name`将被用于创建每个应用程序的应用程序名称重写。
* `server.port`默认为端口80/443。 如果应用其他任何值，则会将其重写为80/443。
* Azure 门户和 Azure 资源管理器模板不支持上传应用程序包。 仅可通过 Azure CLI 部署应用程序来上载应用程序包。

### <a name="what-pricing-tiers-are-available"></a>提供哪些定价层？ 
应使用哪一种，每个层中的限制是多少？
* Azure 春季云提供两个定价层：基本和标准。 基本层面向开发/测试和试用 Azure 春季 Cloud。 标准层经过优化，可运行一般用途的生产流量。 有关限制和功能级别的比较，请参阅[Azure 春季云定价详细信息](https://azure.microsoft.com/pricing/details/spring-cloud/)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供反馈和报告问题？

如果在 Azure 春季云中遇到任何问题，请创建[Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 若要提交功能请求或提供反馈，请参阅[Azure 反馈](https://feedback.azure.com/forums/34192--general-feedback)。

## <a name="development"></a>开发

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是一名云开发人员，而是 Azure 的新手。 了解如何开发 Azure 春季云应用程序的最快方法是什么？

若要快速开始使用 Azure 春季云，请按照[快速入门：使用 Azure 门户启动 Azure 春季 cloud 应用程序](spring-cloud-quickstart-launch-app-portal.md)中的说明进行操作。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支持哪些 Java 运行时？

Azure Spring Cloud 支持 Java 8 和 11。 请参阅[Java 运行时和操作系统版本](#java-runtime-and-os-versions)

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>在哪里可以查看春季 Cloud 应用程序日志和指标？

在“应用概述”选项卡和“[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)”选项卡中可以找到指标。

Azure 春季云支持将春季 Cloud 应用程序日志和指标导出到 Azure 存储、EventHub 和[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的表名为*AppPlatformLogsforSpring*。 若要了解如何启用它，请参阅[诊断服务](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支持分布式跟踪？

是的。 有关详细信息，请参阅[教程：在 Azure 春季云中使用分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服务绑定支持哪些资源类型？

目前支持三个服务：
* Azure Cosmos DB
* Azure Database for MySQL
* Azure Redis 缓存。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>能否在我的应用程序中查看、添加或移动永久性卷？

是的。

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>删除/移动 Azure 春季云服务实例时，是否也会删除/移动其扩展资源？

这取决于拥有扩展资源的资源提供程序的逻辑。 实例的扩展资源 `Microsoft.AppPlatform` 不属于同一命名空间，因此行为因资源提供程序而异。 例如，删除/移动操作不会级联到**诊断设置**资源。 如果使用与删除的资源 ID 相同的资源 ID 预配新的 Azure 春季云实例，或者以前的 Azure 春季云实例被移回，则之前的**诊断设置**资源将继续扩展它。

## <a name="java-runtime-and-os-versions"></a>Java 运行时和操作系统版本

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure 春季云中支持哪些版本的 Java 运行时？

Azure 春季云支持具有最新版本的 Java LTS 版本，当前为2020年6月、Java 8 build 252 和 Java 11 build 7 提供支持。 请参阅[安装适用于 Azure 的 JDK 和 Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>谁构建了这些 Java 运行时？

Azul 系统。 Azul Zulu for Azure - Enterprise Edition 内部版 JDK 是适用于 Azure 和 Azure Stack 的 OpenJDK 的免费、多平台、生产就绪型发行版，由 Microsoft 及 Azul Systems 提供支持。 这些版本包含构建和运行 Java SE 应用程序所需的所有组件。

### <a name="how-often-will-java-runtimes-get-updated"></a>Java 运行时的更新频率是多少？

这些 LTS 和 MTS JDK 版本提供季度安全更新程序和 Bug 修补程序，并根据需要提供关键的带外更新和修补程序。 此支持包括更新版 Java（例如 Java 11）中报告的针对 Java 7 和 8 的安全更新和 Bug 修复的后向移植。

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Java 8 和 Java 11 LTS 版本将支持多长时间？

请参阅[Azure 和 Azure Stack 的 Java 长期支持](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)。

* 在2030年12月之前，将支持 Java 8 LTS。
* Java 11 LTS 将受支持，直到2027年9月。

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>如何下载支持的 Java 运行时以进行本地开发？

请参阅[安装适用于 Azure 的 JDK 和 Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)。

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>对于较旧的 Java 运行时，停用策略是什么？

公开通知将在任何旧的运行时版本停用之前12个月发出。 你将在12个月内迁移到更高版本。

* 订阅管理员将在我们停用 Java 版本后收到电子邮件通知。
* 将在文档中发布停用信息。

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>如何获取 Java 运行时级别的问题支持？

可以通过 Azure 支持开支持票证。  请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

### <a name="what-is-the-operation-system-to-run-my-apps"></a>运行应用的操作系统是什么？

使用最新的 Ubuntu LTS 版本，当前[ubuntu 20.04 LTS （焦距 Fossa）](https://releases.ubuntu.com/focal/)是默认操作系统。

### <a name="how-often-will-os-security-patches-be-applied"></a>OS 安全修补程序的应用频率是多少？

适用于 Azure 春季云的安全修补程序将在每月的基础推出。
适用于 Azure 春季云的关键安全修补程序（CVE 评分 >= 9）将尽快推出。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季云是否支持蓝绿色部署？
是的。 有关详细信息，请参阅[设置过渡环境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>是否可以访问 Kubernetes 来操作应用程序容器？

不能。  Azure Spring Cloud 可使开发人员抽身于底层体系结构，将注意力放在应用程序代码和业务逻辑上。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支持从源构建容器？

是的。 有关详细信息，请参阅[从源代码启动春季云应用程序](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支持应用实例中的自动缩放？

不能。

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

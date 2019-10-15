---
title: Azure Spring Cloud 故障排除指南 | Microsoft Docs
description: Azure Spring Cloud 的故障排除指南
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ebb960085691206b096090813636ef56366e6536
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038356"
---
# <a name="troubleshooting-guide-for-common-problems"></a>解决常见问题的故障排除指南

本文为使用 Azure Spring Cloud 的开发人员详细描述一些常见问题和故障排除步骤。 建议另外阅读我们的[常见问题解答文章](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、性能和应用程序问题

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>我的应用程序无法启动（例如，无法连接终结点，或者在重试几次后返回 502）

将日志导出到 _Azure Log Analytics_。 Spring 应用程序日志的表名为 `AppPlatformLogsforSpring`。 有关详细信息，请访问[使用诊断设置分析日志和指标](diagnostic-services.md)

如果在日志中看到以下错误，则可能表示出现了以下两个问题之一：

`org.springframework.context.ApplicationContextException: Unable to start web server`

* 某个 bean 或其依赖项之一缺失。
* 某个 bean 属性缺失或无效。 在这种情况下，你可能会看到 `java.lang.IllegalArgumentException`。

服务绑定也可能会导致应用程序启动失败。 请使用与绑定的服务相关的关键字来查询日志。  例如，假设应用程序已绑定到设置为本地系统时间的 MySQL 实例。 如果该应用程序无法启动，你可能会在日志中看到以下错误：

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

若要修复此错误，请转到 MySql 实例的 `server parameters`，并将 `time_zone` 从 `SYSTEM` 更改为 `+0:00`。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的应用程序崩溃或引发意外错误

调试应用程序崩溃时，请先检查应用程序的运行状态和发现状态。 在 Azure 门户中转到“应用管理”，确保所有应用程序的状态为“正在运行”和“已启动”。   

* 如果应用程序状态为“正在运行”，但发现状态不是“已启动”，请转到“无法注册我的应用程序”。   [](#my-application-cannot-be-registered)

* 如果发现状态为“已启动”，请转到“指标”检查应用程序的运行状况。   检查以下指标：


  - `TomcatErrorCount` (_tomcat.global.error_)：在这里，所有 Spring 应用程序异常都会计入。 如果此数字很大，请转到“Azure Log Analytics”检查应用程序日志。 

  - `AppMemoryMax` (_jvm.memory.max_)：应用程序可用的最大内存量。 可能未定义此数量，或者在定义后发生了变化。 使用的和提交的内存量将始终少于或等于最大内存量（如果已定义）。 但是，如果在进行内存分配时尝试提高使用的内存，使得使用的内存 > 提交的内存，则即使使用的内存 <= 最大内存，内存分配也会失败，并出现 `OutOfMemoryError` 错误。 在这种情况下，请尝试通过 `-Xmx` 参数提高最大堆栈大小。

  - `AppMemoryUsed` (_jvm.memory.used_)：应用程序目前使用的内存量（以字节为单位）。 对于正常负载的 Java 应用程序，此指标系列将形成一个“锯齿”模式，即内存使用量先是小幅度地平稳增减，然后突然大幅度下降。此模式会重复。 这是因为在 Java 虚拟机中进行的垃圾回收，其中的收集操作体现在“锯齿”模式的下降部分。
    此指标很重要，用于确定各种内存问题，例如：* 在刚开始时内存爆炸；* 为特定逻辑路径分配的内存激增；* 内存缓慢泄漏

  有关更多详细信息，请访问[指标](spring-cloud-concept-metrics.md)。

访问[此入门文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)来开始使用 _Azure Log Analytics_。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的应用程序的 CPU 使用率或内存使用率过高

如果应用程序的 CPU/内存使用率较高，则表示出现了以下两个问题之一：
* 所有应用实例的 CPU/内存使用率较高，或者
* 部分应用实例的 CPU/内存使用率较高。

若要确认出现的是哪种情况，请执行以下操作：

1. 转到“指标”，  选择`Service CPU Usage Percentage`或`Service Memory Used`；
2. 添加 `App=` 筛选器，指定要监视哪个应用程序；
3. 按 `Instance` 拆分指标

如果所有实例的 CPU/内存使用率较高，则需要横向扩展应用程序或纵向扩展 CPU/内存。 有关更多详细信息，请访问[缩放应用程序](spring-cloud-tutorial-scale-manual.md)

如果部分实例的 CPU/内存使用率较高，请检查实例状态及其发现状态。

有关更多详细信息，请访问[指标](spring-cloud-concept-metrics.md)。

如果所有实例都已启动并运行，请转到“Azure Log Analytics”，以查询应用程序日志并查看代码逻辑，看看它们是否影响缩放分区  。 有关更多详细信息，请访问[使用诊断设置分析日志和指标](diagnostic-services.md)。

访问[此入门文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)来开始使用 _Azure Log Analytics_。 使用 [Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>在将 Spring 应用程序加入 Azure Spring Cloud 之前使用的检查清单

* 应用程序可以使用指定的 Java 运行时版本在本地运行。
* 环境配置（CPU/RAM/实例）符合应用程序提供商规定的最低要求。
* 配置项有预期的值。 有关详细信息，请参阅[配置服务器](spring-cloud-tutorial-config-server.md)。
* 环境变量使用预期的值。
* JVM 参数使用预期的值。
* 建议禁用/删除应用程序包中的嵌入式_配置服务器_和 _Spring 服务注册表_服务。
* 若要通过服务绑定来绑定 Azure 资源，请确保目标资源已启动并运行。 

## <a name="configuration-and-management"></a>配置和管理

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>我在创建 Azure Spring Cloud 服务实例时遇到了问题

当你尝试通过门户预配 _Azure Spring Cloud_ 服务实例时，Azure Spring Cloud 将为你执行验证。

但是，如果你尝试通过 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 或[资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/)预配 _Azure Spring Cloud_ 服务实例，请确认：

* 订阅处于活动状态。
* 位置受 _Azure Spring Cloud_ 的[支持](spring-cloud-faq.md)。
* 已创建实例的资源组。
* 资源名称符合命名规则。 （该名称只能包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 值长度必须为 2 到 32 个字符。）

如果你尝试通过资源管理器模板预配 _Azure Spring Cloud_ 服务实例，请访问 https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates 来检查模板语法。

_Azure Spring Cloud_ 服务实例的名称将用于请求 `azureapps.io` 下的子域名，因此，如果该名称与现有名称冲突，预配操作会失败。 可以在活动日志中找到更多详细信息。

### <a name="i-cannot-deploy-a-jar-package"></a>无法部署 JAR 包

无法通过门户或资源管理器模板上传 JAR/源包。

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 部署应用程序包时，CLI 会定期轮询部署进度，并最终显示部署结果。

如果轮询中断，仍可使用以下命令提取部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

确保应用程序打包时采用正确的[可执行 jar 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)。 否则会看到如下所示的错误：

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>无法部署源包

无法通过门户或资源管理器模板上传 JAR/源包。

通过 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 部署应用程序包时，它会定期轮询部署进度，并最终显示部署结果。

如果轮询中断，仍可使用以下命令提取生成和部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

但请注意，一个 _Azure Spring Cloud_ 服务实例每次只能对一个源包触发一个生成作业。 有关更多详细信息，请参阅[部署应用程序](spring-cloud-quickstart-launch-app-portal.md)和[过渡环境指南](spring-cloud-howto-staging-environment.md)。

### <a name="my-application-cannot-be-registered"></a>我的应用程序无法注册

在大多数情况下，如果未在 POM 文件中正确配置所需的依赖项/服务发现，则会发生这种情况。 配置后，内置的服务注册表服务器终结点将作为环境变量注入到应用程序中。 然后，应用程序会将自身注册到服务注册表服务器，并发现其他依赖性微服务。

等待至少 2 分钟，然后新注册的实例就会开始接收流量。

如果要将现有的基于 Spring Cloud 的解决方案迁移到 Azure，请确保删除（或禁用）临时的_服务注册表_和_配置服务器_实例，以免与 _Azure Spring Cloud_ 提供的托管实例冲突。

还可以在 _Azure Log Analytics_ 中检查_服务注册表_客户端日志。 有关更多详细信息，请访问[使用诊断设置分析日志和指标](diagnostic-services.md)

访问[此入门文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)来开始使用 _Azure Log Analytics_。 使用 [Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>我找不到应用程序的指标或日志

转到“应用管理”，确保应用程序的状态为“正在运行”和“已启动”。   

如果可以看到来自 _JVM_ 的指标，但看不到来自 _Tomcat_ 的指标，请检查 `spring-boot-actuator` 依赖项是否已在应用程序包中启用并已成功启动。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果应用程序日志可以存档到某个存储帐户，但未发送到 _Azure Log Analytics_，请检查是否已[正确设置工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果使用的是免费层 _Azure Log Analytics_，请注意，[免费层不提供 SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。
---
title: Azure 春季 Cloud 故障排除指南 |Microsoft Docs
description: Azure 春季云故障排除指南
author: jpconnock
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 9603f4a687b55f45be2875ccaa7b801c0c5589c9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607624"
---
# <a name="troubleshooting-guide-for-common-problems"></a>常见问题故障排除指南

本文详细介绍了在 Azure 春季云中工作的开发人员的一些常见问题和故障排除步骤。 我们还建议阅读我们的[常见问题](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、性能和应用程序问题

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>我的应用程序无法启动（例如，终结点无法连接，或在几次重试后返回502）

将日志导出到_Azure Log Analytics_。 春季应用程序日志的表名为 `AppPlatformLogsforSpring`。 若要了解详细信息，请访问[利用诊断设置分析日志和指标](diagnostic-services.md)

在日志中找到以下错误会指出两个可能的问题之一：

`org.springframework.context.ApplicationContextException: Unable to start web server`

* 缺少一个 bean 或其依赖项之一。
* 某个 bean 属性缺失或无效。 在这种情况下，你可能会看到 `java.lang.IllegalArgumentException`。

服务绑定也可能会导致应用程序启动失败。 使用与绑定服务相关的关键字来查询日志。  例如，假设你的应用程序具有到设置为 "本地系统时间" 的 MySQL 实例的绑定。 如果应用程序无法启动，则可能会在日志中看到以下错误：

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

若要修复此错误，请转到 MySql 实例的 `server parameters`，并将 `time_zone` 从 `SYSTEM` 更改为 `+0:00`。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的应用程序崩溃或引发意外错误

调试应用程序崩溃时，请先检查应用程序的运行状态和发现状态。 请在 Azure 门户中转到 "_应用管理_ _"，以_确保所有应用程序都在_运行和运行_。

* 如果状态为 "_正在运行_" 但发现状态为 "未_启动_"，则[无法注册到 "我的应用程序](#my-application-cannot-be-registered)"。

* 如果发现状态为 "已_启动_"，请参阅 "_指标_" 以检查应用程序的运行状况。 检查以下指标：


  - `TomcatErrorCount` （_tomcat_）：在此处计算所有弹簧应用程序异常。 如果此数字较大，请在_Azure Log Analytics_中检查应用程序日志。

  - `AppMemoryMax` （_jvm_）：可用于应用程序的最大内存量。 如果定义，它可能未定义或更改一段时间。 已使用和已提交内存的数量将始终小于或等于最大值（如果已定义）。 但是，如果内存分配尝试增加已使用的内存（即使已使用 >，即使使用了 < = max），则内存分配可能会失败，并 `OutOfMemoryError`。 在这种情况下，请尝试通过 `-Xmx` 参数增加最大堆大小。

  - `AppMemoryUsed` （_jvm_）：应用程序当前使用的内存量（以字节为单位）。 对于普通的负载 Java 应用程序，此指标系列将形成一个 "呈现锯齿" 模式，其中内存使用持续增加并减小，并突然中断，此模式将重复。 这是因为 Java 虚拟机内的垃圾回收，其中集合操作表示 "sawteeth" 上的 "删除"。
    此指标对于识别内存问题很重要，例如： * 特定逻辑路径的最开始时间 * 的内存爆炸

  有关更多详细信息，请访问[指标](spring-cloud-concept-metrics.md)。

请访问[此入门文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)，开始了解_Azure Log Analytics_。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的应用程序遇到 CPU 使用率高或内存使用率高

如果你的应用程序遇到高 CPU/内存使用情况，则需要满足以下两个条件之一：
* 所有应用程序实例都遇到高 CPU/内存使用率，或
* 某些应用实例遇到高 CPU/内存使用率。

若要确认是哪种情况，

1. 请参阅 "_指标_"，选择 `Service CPU Usage Percentage` 或 `Service Memory Used`。
2. 添加 `App=` 筛选器以指定你要监视的应用程序，以及
3. 按 `Instance`拆分度量值。

如果所有实例都遇到高 CPU/内存，则需要向外扩展应用程序或扩展 CPU/内存。 有关更多详细信息，请访问[缩放应用程序](spring-cloud-tutorial-scale-manual.md)

如果某些实例遇到高 CPU/内存，请检查实例状态及其发现状态。

有关更多详细信息，请访问[指标](spring-cloud-concept-metrics.md)。

如果所有实例均已启动并正在运行，请访问_Azure Log Analytics_以查询应用程序日志，并查看代码逻辑，以查看是否有任何这些实例可能会影响规模分区。 有关更多详细信息，请参阅[通过诊断设置分析日志和指标](diagnostic-services.md)。

请访问[此入门文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)，开始了解_Azure Log Analytics_。 使用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>将春季应用程序载入 Azure 春季云之前的清单

* 应用程序可以在本地运行指定的 Java 运行时版本。
* 环境配置（CPU/RAM/实例）满足应用程序提供程序设置的最低要求。
* 配置项目具有预期值。 有关详细信息，请参阅[Config Server](spring-cloud-tutorial-config-server.md)。
* 环境变量具有预期值。
* JVM 参数包含所需的值。
* 建议禁用/删除应用程序包中的嵌入式_配置服务器_和_弹簧服务注册表_服务。
* 如果要通过_服务绑定_来绑定任何 Azure 资源，请确保目标资源已启动并正在运行。

## <a name="configuration-and-management"></a>配置和管理

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>我在创建 Azure 春季云服务实例时遇到问题

尝试通过门户预配_Azure 春季云_服务实例时，Azure 春季 cloud 会执行验证。

但是，如果尝试通过[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)或[资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/)来设置_Azure 春季云_服务实例，请验证：

* 订阅处于活动状态。
* _Azure 春季 Cloud_[支持](spring-cloud-faq.md)此位置。
* 已创建实例的资源组。
* 资源名称符合命名规则。 （它只能包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 值的长度必须介于2到32个字符之间。）

如果尝试通过资源管理器模板设置_Azure 春季云_服务实例，请访问 https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates 以检查模板语法。

_Azure 春季云_服务实例的名称将用于请求 `azureapps.io`下的子域名称，以便在名称与现有名称冲突时，设置将失败。 你可能会在活动日志中找到更多详细信息。

### <a name="i-cannot-deploy-a-jar-package"></a>我无法部署 JAR 包

不能通过门户或资源管理器模板上传 JAR/源包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署应用程序包时，它会定期轮询部署进度，并在结束时显示部署结果。

如果轮询中断，仍可使用以下命令提取部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

确保应用程序以正确的[可执行 jar 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)打包。 否则，你将看到如下错误：

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>我无法部署源包

不能通过门户或资源管理器模板上传 JAR/源包。

部署[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)的应用程序包时，它会定期轮询部署进度，最终会显示部署结果。

如果轮询中断，仍可使用以下命令提取生成和部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

但请注意，一个_Azure 春季云_服务实例一次只能触发一个源包的生成作业。 有关更多详细信息，请参阅[部署应用程序](spring-cloud-quickstart-launch-app-portal.md)和[过渡环境指南](spring-cloud-howto-staging-environment.md)。

### <a name="my-application-cannot-be-registered"></a>我的应用程序无法注册

在大多数情况下，在 POM 文件中未正确配置所需的依赖项/服务发现时，会发生这种情况。 配置后，内置的服务注册表服务器终结点将作为环境变量注入到您的应用程序。 然后，应用程序将向服务注册表服务器注册自己，并发现其他依赖微服务。

在新注册的实例开始接收流量之前至少等待2分钟。

如果要将现有的基于春季云的解决方案迁移到 Azure，请确保已删除（或禁用）即席_服务注册表_和_配置服务器_实例，以避免与_Azure 春季云中提供的托管实例发生冲突_.

你还可以在_Azure Log Analytics_中查看_服务注册表_客户端日志。 有关更多详细信息，请参阅[通过诊断设置分析日志和指标](diagnostic-services.md)

请访问[此入门文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)，开始了解_Azure Log Analytics_。 使用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我要检查应用程序的环境变量

环境变量通知 Azure 春季 Cloud framework，确保 Azure 了解在何处以及如何配置构成应用程序的服务。  确保环境变量是正确的，这是解决潜在问题的第一步。  可以使用春季 Boot 制动器终结点来查看环境变量。  

> [!WARNING]
> 此过程使用测试终结点公开环境变量。  如果测试终结点可公开访问，或者已为应用程序分配了域名，请不要继续操作。

1. 导航到此 URL： "`https://<your application test endpoint>/actuator/health`"。  
    - 与 `{"status":"UP"}` 类似的响应指示已启用终结点。
    - 如果响应为负数，请在 `POM.xml`中包括以下依赖项：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 启用春季 Boot 传动终结点后，请跳到 Azure 门户并找到应用程序的配置页。  添加一个名为 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 的环境变量，并将值 `*`。 

1. 重新启动应用程序。

1. 导航到 `https://<the test endpoint of your app>/actuator/env` 并检查响应。  它看起来应该如下所示：

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

查找名为 `systemEnvironment`的子节点。  此节点包含应用程序的环境变量。

> [!IMPORTANT]
> 在使应用程序可供公众访问之前，请务必反转环境变量的公开。  请参阅 Azure 门户，找到应用程序的 "配置" 页，并删除此环境变量： "`MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`"。

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>我找不到应用程序的指标或日志

请切换到 "_应用管理_"，确保应用程序正在运行，并且_运行_ _正常_。

如果你可以看到来自_JVM_的指标，但却没有来自_Tomcat_的指标，请检查你的应用程序包中是否启用了`spring-boot-actuator` 依赖关系并成功启动。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果可以将应用程序日志存档到存储帐户，但不能将其发送到_Azure Log Analytics_，请检查是否[正确设置了工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果使用的是免费的_Azure Log Analytics_层，请注意，[免费级别不会提供 SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。
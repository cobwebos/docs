---
title: Azure Spring Cloud 故障排除指南 | Microsoft Docs
description: Azure Spring Cloud 的故障排除指南
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277583"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>解决常见的 Azure 春云问题

本文提供有关解决 Azure 春云开发问题疑难解答的说明。 有关详细信息，请参阅[Azure 春云常见问题解答](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、性能和应用程序问题

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>我的应用程序无法启动（例如，终结点无法连接，或者经过几次重试后返回 502）

将日志导出到 Azure Log Analytics。 Spring 应用程序日志的表名为*AppPlatformLogsforSpring*。 要了解更多信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)。

日志中可能会出现以下错误消息：

> "org.springframework.上下文.应用程序上下文例外：无法启动 Web 服务器"

该消息指示两个可能的问题之一： 
* 某个 bean 或其依赖项之一缺失。
* 某个 bean 属性缺失或无效。 在这种情况下，可能会显示"java.lang.非法参数例外"。

服务绑定还可能导致应用程序启动失败。 要查询日志，请使用与绑定服务相关的关键字。 例如，假设应用程序具有设置为本地系统时间的 MySQL 实例的绑定。 如果应用程序无法启动，日志中可能会出现以下错误消息：

> java.sql.SQLException：服务器时区值"协调通用时间"无法识别或表示多个时区。

要修复`server parameters`此错误，请转到 MySQL 实例，并将`time_zone`值从*SYSTEM*更改为 *+0：00*。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的应用程序崩溃或引发意外错误

调试应用程序崩溃时，请从检查应用程序的运行状态和发现状态开始。 为此，请访问 Azure 门户中的应用_管理_，以确保所有应用程序的状态都_处于运行_状态并_UP_。

* 如果状态为 _"正在运行_，但发现状态不是[UP"，请转到"无法注册我的应用程序"](#my-application-cant-be-registered)部分。 _UP_

* 如果发现状态为“已启动”，请转到“指标”检查应用程序的运行状况。__ 检查以下指标：


  - `TomcatErrorCount`_（tomcat.global.error）：_ 所有春季应用程序异常都在这里计算。 如果此数字很大，请转到“Azure Log Analytics”检查应用程序日志。

  - `AppMemoryMax`_（jvm.memory.max）：_ 应用程序可用的最大内存量。 金额可能未定义，如果定义金额，则可能会随时间而变化。 如果已定义，则使用和提交的内存量始终小于或等于最大值。但是，如果分配尝试增加*已使用的*内存`OutOfMemoryError`（>提交）时，即使*使用<= 最大值*仍为 true，则消息可能会失败。 在这种情况下，请尝试使用`-Xmx`参数增加最大堆大小。

  - `AppMemoryUsed`_（jvm.memory.使用_）：应用程序当前使用的内存量（以字节为单位）。 对于普通负载 Java 应用程序，此指标系列形成*锯齿*模式，其中内存使用量以较小的增量稳步增加和减少，并突然下降很多，然后模式重复出现。 此指标系列发生的原因是 Java 虚拟机中的垃圾回收，其中收集操作表示锯齿模式上的丢弃。
    
    此指标对于帮助识别内存问题非常重要，例如：
    * 一开始的记忆爆炸。
    * 特定逻辑路径的浪涌内存分配。
    * 逐渐泄漏内存。

  有关详细信息，请参阅[指标](spring-cloud-concept-metrics.md)。

要了解有关 Azure 日志分析的更多信息，请参阅在[Azure 监视器 中开始使用日志分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的应用程序的 CPU 使用率或内存使用率过高

如果应用程序 CPU 或内存使用率高，则其中一项是正确的：
* 所有应用实例都体验高 CPU 或内存使用率。
* 某些应用实例的 CPU 或内存使用率很高。

要确定适用哪种情况，请执行以下操作：

1. 转到**指标**，然后选择服务 CPU**使用率或****已使用的服务内存**。
2. 添加**App+** 筛选器以指定要监视的应用程序。
3. 按**实例**拆分指标。

如果*所有实例*都遇到高 CPU 或内存使用率，则需要扩展应用程序或扩展 CPU 或内存使用量。 有关详细信息，请参阅[教程：在 Azure 春云中缩放应用程序](spring-cloud-tutorial-scale-manual.md)。

如果*某些实例*CPU 或内存使用率较高，请检查实例状态及其发现状态。

有关详细信息，请参阅[Azure 春云的指标](spring-cloud-concept-metrics.md)。

如果所有实例都已启动并运行，请转到 Azure 日志分析以查询应用程序日志并查看代码逻辑。 这将有助于您了解其中是否有任何可能会影响比例分区。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)。

要了解有关 Azure 日志分析的更多信息，请参阅在[Azure 监视器 中开始使用日志分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>将春季应用程序部署到 Azure 春云的检查表

在将应用程序加入之前，请确保它符合以下条件：

* 应用程序可以使用指定的 Java 运行时版本在本地运行。
* 环境配置（CPU/RAM/实例）符合应用程序提供商规定的最低要求。
* 配置项具有其预期值。 有关详细信息，请参阅[配置服务器](spring-cloud-tutorial-config-server.md)。
* 环境变量具有其预期值。
* JVM 参数具有其预期值。
* 我们建议您禁用或删除应用程序包中嵌入的_Config 服务器_和_Spring 服务注册表_服务。
* 若要通过服务绑定来绑定 Azure 资源，请确保目标资源已启动并运行。__

## <a name="configuration-and-management"></a>配置和管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>创建 Azure Spring 云服务实例时遇到问题

使用 Azure 门户设置 Azure 春云服务实例时，Azure 春云会为您执行验证。

但是，如果您尝试使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)或[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/)设置 Azure 春风云服务实例，请验证：

* 订阅处于活动状态。
* Azure 春云[支持](spring-cloud-faq.md)该位置。
* 已创建实例的资源组。
* 资源名称符合命名规则。 它必须仅包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 该值必须包含从 2 到 32 个字符。

如果要使用资源管理器模板设置 Azure 春云服务实例，请首先参考[了解 Azure 资源管理器模板的结构和语法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。

Azure Spring Cloud 服务实例的名称将用于在 下`azureapps.io`请求子域名，因此，如果名称与现有名称冲突，则设置将失败。 您可以在活动日志中找到更多详细信息。

### <a name="i-cant-deploy-a-jar-package"></a>无法部署 JAR 包

无法使用 Azure 门户或资源管理器模板上载 Java 存档文件 （JAR）/源包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署应用程序包时，Azure CLI 会定期轮询部署进度，最后显示部署结果。

如果轮询中断，仍可使用以下命令提取部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

确保您的应用程序以正确的[可执行 JAR 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)打包。 如果未正确打包，您将收到类似于以下内容的错误消息：

> "Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>无法部署源包

不能使用 Azure 门户或资源管理器模板上载 JAR/源包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署应用程序包时，Azure CLI 会定期轮询部署进度，最后显示部署结果。

如果轮询中断，仍可使用以下命令提取生成和部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

但是，请注意，一个 Azure Spring Cloud 服务实例一次只能触发一个源包的一个生成作业。 有关详细信息，请参阅在 Azure Spring Cloud 中[部署应用程序](spring-cloud-quickstart-launch-app-portal.md)并[设置暂存环境](spring-cloud-howto-staging-environment.md)。

### <a name="my-application-cant-be-registered"></a>无法注册我的应用程序

在大多数情况下，当项目对象模型 （POM） 文件中未正确配置*所需的依赖项**和服务发现*时，将发生这种情况。 配置后，内置服务注册表服务器终结点将作为环境变量随应用程序一起注入。 然后，应用程序在服务注册表服务器注册自己，并发现其他从属微服务。

等待至少两分钟，然后新注册的实例开始接收流量。

如果要将现有的基于 Spring Cloud 的解决方案迁移到 Azure，请确保删除（或禁用）临时_服务注册表_和_配置服务器_实例，以避免与 Azure Spring Cloud 提供的托管实例发生冲突。

您还可以在 Azure 日志分析中检查_服务注册表_客户端日志。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)

要了解有关 Azure 日志分析的更多信息，请参阅在[Azure 监视器 中开始使用日志分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要检查应用程序的环境变量

环境变量通知 Azure 春云框架，确保 Azure 了解配置构成应用程序的服务的位置以及如何。 在排查潜在问题的过程中，必须执行的第一步是确保环境变量正确。  可以使用 Spring Boot Actuator 终结点来查看环境变量。  

> [!WARNING]
> 此过程通过使用测试终结点公开环境变量。  如果测试终结点可以公开访问，或者你已将域名分配给应用程序，请勿继续操作。

1. 转到  `https://<your application test endpoint>/actuator/health` 。  
    - 类似于 `{"status":"UP"}` 的响应表明终结点已启用。
    - 如果响应为负，请在*POM.xml*文件中包括以下依赖项：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 启用 Spring Boot 执行器终结点后，请转到 Azure 门户并查找应用程序的配置页。  添加名称`MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`和值`*`的环境变量。 

1. 重启应用程序。

1. 转到`https://<your application test endpoint>/actuator/env`并检查响应。  它看起来应该如下所示：

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

查找名为`systemEnvironment`的子节点。  此节点包含应用程序的环境变量。

> [!IMPORTANT]
> 在使应用程序可以公共访问之前，请记得反转公开环境变量的操作。  转到 Azure 门户，查找应用程序的配置页，然后删除此环境变量： `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>我找不到应用程序的指标或日志

转到**应用程序管理**以确保应用程序状态_正在运行_和_UP。_

如果可以看到_JVM_中的指标，但_Tomcat_没有指标，请检查在应用程序包中`spring-boot-actuator`是否启用了依赖项，以及它是否成功启动。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果应用程序日志可以存档到存储帐户，但不能发送到 Azure 日志分析，请检查是否[正确设置工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果使用 Azure 日志分析的免费层，请注意[，免费层不提供服务级别协议 （SLA）。](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)

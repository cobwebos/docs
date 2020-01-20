---
title: Azure Spring Cloud 故障排除指南 | Microsoft Docs
description: Azure Spring Cloud 的故障排除指南
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277583"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>排查常见的 Azure 春季云问题

本文提供了有关排查 Azure 春季云开发问题的说明。 有关其他信息，请参阅[Azure 春季 CLOUD 常见问题解答](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、性能和应用程序问题

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>我的应用程序无法启动（例如，终结点无法连接，或在几次重试后返回502）

将日志导出到 Azure Log Analytics。 春季应用程序日志的表名为*AppPlatformLogsforSpring*。 若要了解详细信息，请参阅[通过诊断设置分析日志和指标](diagnostic-services.md)。

日志中可能会出现以下错误消息：

> "springframework： ApplicationContextException：无法启动 web 服务器"

此消息表示两个可能的问题之一： 
* 某个 bean 或其依赖项之一缺失。
* 某个 bean 属性缺失或无效。 在这种情况下，可能会显示 "IllegalArgumentException"。

服务绑定可能也会导致应用程序启动失败。 若要查询日志，请使用与绑定服务相关的关键字。 例如，假设你的应用程序具有到设置为 "本地系统时间" 的 MySQL 实例的绑定。 如果应用程序无法启动，日志中可能会出现以下错误消息：

> "SQLException：服务器时区值" 协调世界时 "无法识别或表示多个时区。"

若要修复此错误，请转到 MySQL 实例的 `server parameters`，并将 `time_zone` 值从 "*系统*" 更改为 *+ 0:00*。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的应用程序崩溃或引发意外错误

调试应用程序崩溃时，请先检查应用程序的运行状态和发现状态。 为此，请在 Azure 门户中转到 "_应用管理_ _"，以_确保所有应用程序的状态是 "_正在运行_" 和 "运行"。

* 如果状态为 "_正在运行_" 但发现状态为 "未_启动_"，请参阅["无法注册我的应用程序"](#my-application-cant-be-registered)部分。

* 如果发现状态为 "已_启动_"，请参阅 "指标" 以检查应用程序的运行状况。 检查以下指标：


  - `TomcatErrorCount` （_tomcat_）：在此处计算所有弹簧应用程序异常。 如果此数字较大，请在 Azure Log Analytics 中检查应用程序日志。

  - `AppMemoryMax` （_jvm_）：可用于应用程序的最大内存量。 数量可能未定义，或者如果定义，则可能会随时间更改。 如果已定义，则所用和已提交的内存量始终小于或等于最大值。但是，如果分配尝试增加已使用的内存（即使已使用 *>* 的内存），则内存分配可能会失败并返回 `OutOfMemoryError` 的消息，即使*使用了 < = max*也是如此。 在这种情况下，请尝试使用 `-Xmx` 参数增加最大堆大小。

  - `AppMemoryUsed` （_jvm_）：应用程序当前使用的内存量（以字节为单位）。 对于普通的 load Java 应用程序，此指标系列形成了一种*呈现锯齿*模式，其中内存使用持续增加并减小，并突然降低，然后模式重复出现。 之所以发生此指标系列，是因为在 Java 虚拟机内进行了垃圾回收，其中集合操作代表呈现锯齿模式下的删除。
    
    此指标有助于识别内存问题，例如：
    * 最开始的内存分解。
    * 特定逻辑路径的冲击内存分配。
    * 逐步内存泄露。

  有关详细信息，请参阅[度量值](spring-cloud-concept-metrics.md)。

若要了解有关 Azure Log Analytics 的详细信息，请参阅[Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的应用程序的 CPU 使用率或内存使用率过高

如果你的应用程序的 CPU 或内存使用率较高，则会出现以下两种情况之一：
* 所有应用程序实例都出现 CPU 或内存使用率过高的情况。
* 某些应用实例的 CPU 或内存使用率很高。

若要确定适用的情况，请执行以下操作：

1. 中转到 "**指标**"，然后选择 "**服务 CPU 使用率百分比**" 或 "已**使用的服务内存**"。
2. 添加**应用 =** 筛选器，以指定要监视的应用程序。
3. 按**实例**拆分度量值。

如果*所有实例*都出现 cpu 或内存使用率较高，则需要向外扩展应用程序或增加 cpu 或内存使用率。 有关详细信息，请参阅[教程：在 Azure 春季云中缩放应用程序](spring-cloud-tutorial-scale-manual.md)。

如果*某些实例*的 CPU 或内存使用率较高，请检查实例状态及其发现状态。

有关详细信息，请参阅[Azure 春季云的指标](spring-cloud-concept-metrics.md)。

如果所有实例均已启动并正在运行，请在 Azure Log Analytics 中查询应用程序日志，并查看代码逻辑。 这将帮助你了解其中是否有任何一个可能会影响缩放分区。 有关详细信息，请参阅[通过诊断设置分析日志和指标](diagnostic-services.md)。

若要了解有关 Azure Log Analytics 的详细信息，请参阅[Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>将弹簧应用程序部署到 Azure 春季云的清单

在载入应用程序之前，请确保它满足以下条件：

* 应用程序可以使用指定的 Java 运行时版本在本地运行。
* 环境配置（CPU/RAM/实例）符合应用程序提供商规定的最低要求。
* 配置项目具有其预期值。 有关详细信息，请参阅[配置服务器](spring-cloud-tutorial-config-server.md)。
* 环境变量具有其预期值。
* JVM 参数具有其预期值。
* 建议你禁用或删除应用程序包中的嵌入式_配置服务器_和_弹簧服务注册表_服务。
* 若要通过服务绑定来绑定 Azure 资源，请确保目标资源已启动并运行。

## <a name="configuration-and-management"></a>配置和管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在创建 Azure 春季云服务实例时遇到问题

使用 Azure 门户设置 Azure 春季云服务实例时，Azure 春季云会为你执行验证。

但如果尝试使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)或[azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/)设置 azure 春季云服务实例，请验证：

* 订阅处于活动状态。
* Azure 春季 Cloud[支持](spring-cloud-faq.md)此位置。
* 已创建实例的资源组。
* 资源名称符合命名规则。 它必须仅包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 该值必须包含2到32个字符。

如果要使用资源管理器模板设置 Azure 春季云服务实例，请首先参阅[了解 Azure 资源管理器模板的结构和语法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。

Azure 春季云服务实例的名称将用于请求 `azureapps.io`下的子域名称，因此如果该名称与现有名称冲突，则安装将失败。 你可能会在活动日志中找到更多详细信息。

### <a name="i-cant-deploy-a-jar-package"></a>无法部署 JAR 包

无法使用 Azure 门户或资源管理器模板上传 Java 存档文件（JAR）/source 包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署应用程序包时，Azure CLI 会定期轮询部署进度，并在最终显示部署结果。

如果轮询中断，仍可使用以下命令提取部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

确保应用程序以正确的[可执行 JAR 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)打包。 如果未正确打包，你将收到类似于以下内容的错误消息：

> "错误：无效或损坏的 jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>无法部署源包

无法使用 Azure 门户或资源管理器模板上传 JAR/源包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署应用程序包时，Azure CLI 会定期轮询部署进度，并在最终显示部署结果。

如果轮询中断，仍可使用以下命令提取生成和部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

但请注意，一个 Azure 春季云服务实例一次只能触发一个源包的生成作业。 有关详细信息，请参阅[在 Azure 春季云中](spring-cloud-howto-staging-environment.md)[部署应用程序](spring-cloud-quickstart-launch-app-portal.md)和设置过渡环境。

### <a name="my-application-cant-be-registered"></a>我的应用程序无法注册

在大多数情况下，在项目对象模型（POM）文件中未正确配置*所需的依赖项*和*服务发现*时，会发生这种情况。 配置后，内置的服务注册表服务器终结点将作为环境变量注入你的应用程序。 然后，应用程序向服务注册表服务器注册自己，并发现其他依赖微服务。

等待至少两分钟后，新注册的实例才会开始接收流量。

如果要将现有的基于云的基于云的解决方案迁移到 Azure，请确保已删除（或禁用）即席_服务注册表_和_配置服务器_实例，以避免与 Azure 春季云中提供的托管实例发生冲突。

你还可以在 Azure Log Analytics 中查看_服务注册表_客户端日志。 有关详细信息，请参阅[通过诊断设置分析日志和指标](diagnostic-services.md)

若要了解有关 Azure Log Analytics 的详细信息，请参阅[Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)查询日志。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要检查应用程序的环境变量

环境变量通知 Azure 春季 Cloud framework，确保 Azure 了解在何处以及如何配置构成应用程序的服务。 在排查潜在问题的过程中，必须执行的第一步是确保环境变量正确。  可以使用 Spring Boot Actuator 终结点来查看环境变量。  

> [!WARNING]
> 此过程使用测试终结点公开环境变量。  如果测试终结点可以公开访问，或者你已将域名分配给应用程序，请勿继续操作。

1. 转到  `https://<your application test endpoint>/actuator/health` 。  
    - 类似于 `{"status":"UP"}` 的响应表明终结点已启用。
    - 如果响应为负数，请在*POM*文件中包含以下依赖项：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 启用春季 Boot 传动终结点后，请跳到 Azure 门户，并查找应用程序的配置页。  添加一个名为 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 的环境变量，并将值 `*`。 

1. 重启应用程序。

1. 中转到 `https://<your application test endpoint>/actuator/env` 并检查响应。  它看起来应该如下所示：

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
> 在使应用程序可以公共访问之前，请记得反转公开环境变量的操作。  请参阅 Azure 门户，查找应用程序的 "配置" 页，并删除此环境变量： "`MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`"。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>找不到应用程序的指标或日志

请中转到 "**应用管理** _"，以_确保应用程序状态为 "_正在运行_" 和 "运行"。

如果看不到来自_JVM_的指标，但你可以看到_Tomcat_中的指标，请检查是否在应用程序包中启用了 `spring-boot-actuator` 依赖关系，以及是否成功启动。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果你的应用程序日志可以存档到存储帐户，但不能发送到 Azure Log Analytics，请检查是否[正确设置了你的工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果使用的是免费的 Azure Log Analytics 层，请注意，[免费级别不提供服务级别协议（SLA）](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。

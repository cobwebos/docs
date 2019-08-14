---
title: 配置 Linux Java 应用-Azure App Service |Microsoft Docs
description: 了解如何配置在 Linux 上的 Azure 应用服务中运行的 Java 应用。
keywords: azure 应用服务, web 应用, linux, oss, java, java ee, jee, javaee
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 825379c04c22b3f13e651455c490a58ad47169d8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967161"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>为 Azure App Service 配置 Linux Java 应用

Linux 上的 Azure 应用服务可让 Java 开发人员在完全托管的基于 Linux 的服务中快速生成、部署和缩放 Tomcat 或 Java Standard Edition (SE) 打包式 Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南为在应用服务中使用内置 Linux 容器的 Java 开发人员提供重要的概念和说明。 如果你从未使用过 Azure App Service, 请先按照[java 快速入门](quickstart-java.md)和[java with PostgreSQL 教程](tutorial-java-enterprise-postgresql-app.md)操作。

## <a name="deploying-your-app"></a>部署应用

你可以使用用于[Azure App Service 的 Maven 插件](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)来部署 .jar 和 war 文件。 [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij)或[Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)还支持通过常用 ide 进行部署。

否则, 你的部署方法将取决于你的存档类型:

- 若要将 .war 文件部署到 Tomcat，请使用 `/api/wardeploy/` 终结点对存档文件执行 POST 操作。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像中的 .jar 文件，请使用 Kudu 站点的 `/api/zipdeploy/` 终结点。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

不要使用 FTP 来部署 .war 或 .jar。 FTP 工具设计用来上传启动脚本、依赖项或其他运行时文件。 它不是用于部署 Web 应用的最佳选项。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 有关详细信息, 请参阅[Azure App Service 诊断概述](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 控制台访问

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>流式传输诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

有关详细信息，请参阅[使用 Azure CLI 流式处理日志](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>应用日志记录

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。 可以在 */home/LogFiles/Application/* 目录中找到 Java 和 Tomcat 应用日志。

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](/azure/application-insights/app-insights-java-trace-logs)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑难解答工具

内置 Java 映像基于[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)操作系统。 `apk`使用包管理器安装任何故障排除工具或命令。

### <a name="flight-recorder"></a>网络流量记录器

应用服务上的所有 Linux Java 映像都已安装了祖鲁网络流量记录器, 因此你可以轻松连接到 JVM 并启动探查器记录或生成堆转储。

#### <a name="timed-recording"></a>定时录制

若要开始, 请通过 SSH 连接到应用服务并`jcmd`运行命令, 以查看所有正在运行的 Java 进程的列表。 除了 jcmd 本身以外, 你还应该看到你的 Java 应用程序运行时使用的是进程 ID 号 (pid)。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

执行以下命令以启动 JVM 的30秒记录。 这会分析 JVM 并在主目录中创建名为*jfr_example JFR*的 JFR 文件。 (将116替换为 Java 应用的 pid。)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在30秒的时间间隔内, 可以通过运行`jcmd 116 JFR.check`来验证录制是否正在进行。 这会显示给定 Java 进程的所有录制。

#### <a name="continuous-recording"></a>连续录制

可以使用祖鲁网络流量记录器来持续分析 Java 应用程序, 对运行时性能 ([源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)) 的影响最小。 为此, 请运行以下 Azure CLI 命令, 以使用必要的配置创建名为 JAVA_OPTS 的应用设置。 当你的应用程序启动时, JAVA_OPTS 应用程序`java`设置的内容将传递给命令。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

开始记录后, 可以使用`JFR.dump`命令随时转储当前记录数据。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

有关详细信息, 请参阅[Jcmd 命令参考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析录制

使用[FTPS](../deploy-ftp.md)将 JFR 文件下载到本地计算机。 若要分析 JFR 文件, 请下载并安装[祖鲁任务控制](https://www.azul.com/products/zulu-mission-control/)。 有关祖鲁任务控制的说明, 请参阅[Azul 文档](https://docs.azul.com/zmc/)和[安装说明](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自定义和优化

适用于 Linux 的 Azure App Service 支持通过 Azure 门户和 CLI 实现全新的优化和自定义。 查看以下文章以了解非 Java 特定的 web 应用配置:

- [配置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [设置自定义域](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [启用 SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [添加 CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [配置 Kudu 站点](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要在 Tomcat 和 Java SE 环境中设置分配的内存或其他 JVM 运行时选项, 请使用选项`JAVA_OPTS`创建一个名为的[应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 应用服务 Linux 在启动时，会将此设置作为环境变量传递给 Java 运行时。

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含其他设置的新应用设置，例如 `-Xms512m -Xmx1204m`。

若要从 Maven 插件配置应用设置, 请在 "Azure 插件" 部分中添加 "设置/值" 标记。 下面的示例设置了一个特定的最小和最大 Java 堆大小:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

在应用服务计划中运行包含一个部署槽位的单个应用程序的开发人员可以使用以下选项：

- B1 和 S1 实例:`-Xms1024m -Xmx1024m`
- B2 和 S2 实例:`-Xms3072m -Xmx3072m`
- B3 和 S3 实例:`-Xms6144m -Xmx6144m`

优化应用程序堆设置时，请查看应用服务计划详细信息，并考虑多个应用程序和部署槽位方面的需求，以得出最佳内存分配。

如果要部署 JAR 应用程序, 该应用程序应命名为*app.config* , 以便内置映像可以正确地识别你的应用。 (Maven 插件会自动进行此重命名。)如果你不希望将 JAR 重命名为*app.config*, 则可以使用命令上传 shell 脚本来运行 JAR。 然后, 将此脚本的完整路径粘贴到门户的 "配置" 部分中的 "[启动文件](app-service-linux-faq.md#built-in-images)" 文本框中。

### <a name="turn-on-web-sockets"></a>启用 Web 套接字

在 Azure 门户中应用程序的“应用程序设置”中启用 Web 套接字支持。 需要重启应用程序才能使设置生效。

在 Azure CLI 中使用以下命令启用 Web 套接字支持：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

然后重启应用程序：

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>设置默认的字符编码

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含值 `-Dfile.encoding=UTF-8` 的新应用设置。

或者，可以使用应用服务 Maven 插件配置应用设置。 在插件配置中添加设置名称和值标记：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>调整启动超时

如果 Java 应用程序特别大, 则应增加启动时间限制。 为此, 请创建一个应用程序设置`WEBSITES_CONTAINER_START_TIME_LIMIT` , 并将其设置为应用服务在超时前应等待的秒数。最大值为`1800`秒。

### <a name="pre-compile-jsp-files"></a>预编译 JSP 文件

若要提高 Tomcat 应用程序的性能, 可以在部署到应用服务之前编译 JSP 文件。 可以使用 Apache 抛开提供的[Maven 插件](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)或使用此[Ant 生成文件](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>安全应用程序

在适用于 Linux 应用服务中运行的 Java 应用程序实施与其他应用程序相同的一套[安全最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users-easy-auth"></a>对用户进行身份验证 (轻松身份验证)

在 Azure 门户中设置采用**身份验证和授权**选项的应用身份验证。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。 有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以及其他标识提供者的相关文章。 如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)一文中的说明。

#### <a name="tomcat-and-wildfly"></a>Tomcat 和 Wildfly

Tomcat 或 Wildfly 应用程序可以通过将主体对象强制转换为 Map 对象, 直接从 servlet 访问用户的声明。 Map 对象会将每个声明类型映射到该类型的声明的集合。 在下面的代码中`request` , 是的`HttpServletRequest`实例。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

现在, 可以检查该`Map`对象是否有任何特定的声明。 例如, 以下代码片段将循环访问所有声明类型, 并打印每个集合的内容。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

若要注销用户, 请使用`/.auth/ext/logout`路径。 若要执行其他操作, 请参阅有关[应用服务身份验证和授权使用](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)的文档。 Tomcat [HttpServletRequest 接口](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法还提供了官方文档。 以下 servlet 方法还基于你的应用服务配置解冻:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要禁用此功能, 请创建一个名`WEBSITE_AUTH_SKIP_PRINCIPAL`为且值为`1`的应用程序设置。 若要禁用应用服务添加的所有 servlet 筛选器, 请创建`WEBSITE_SKIP_FILTERS`一个名为且`1`值为的设置。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 开发人员可以使用 [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) 通过熟悉的 Spring Security 注释和 API 来保护应用程序。 请确保在*应用程序*中增加最大标头大小。 我们建议值为 `16384`。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

遵照[绑定现有的自定义 SSL 证书](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)中的说明上传现有的 SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 引用

[Azure KeyVault](../../key-vault/key-vault-overview.md)使用访问策略和审核历史记录提供集中式密钥管理。 可以在 KeyVault 中存储机密 (如密码或连接字符串), 并通过环境变量访问应用程序中的这些机密。

首先, 按照说明向应用程序[授予对 Key Vault 的访问权限](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault), 并[在应用程序设置中对机密进行 KeyVault 引用](../app-service-key-vault-references.md#reference-syntax)。 可以通过在远程访问应用服务终端时打印环境变量来验证引用是否解析为机密。

若要在弹簧或 Tomcat 配置文件中注入这些机密, 请使用环境变量注入`${MY_ENV_VAR}`语法 ()。 对于春季配置文件, 请参阅此文档中的[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)。

## <a name="configure-apm-platforms"></a>配置 APM 平台

本部分演示如何将 Linux 上的 Azure App Service 上部署的 Java 应用程序与 NewRelic 和 AppDynamics 应用程序性能监视 (APM) 平台连接起来。

### <a name="configure-new-relic"></a>配置 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 NewRelic 帐户
2. 从 NewRelic 下载 Java agent 后, 它将具有类似于*newrelic-java-x*. x. x. x. x. x. x. x. x. x。
3. 复制你的许可证密钥，稍后需要使用它来配置代理。
4. 通过[SSH 连接到应用服务实例](app-service-linux-ssh-support.md), 并创建新的目录 */home/site/wwwroot/apm*。
5. 将解压缩的 NewRelic Java 代理文件上传到 */home/site/wwwroot/apm*下的目录中。 代理的文件应在 */home/site/wwwroot/apm/newrelic*中。
6. 修改 */home/site/wwwroot/apm/newrelic/newrelic.yml*上的 YAML 文件, 并将占位符许可证值替换为你自己的许可密钥。
7. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你的应用使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果使用的是**WildFly**, 请参阅[此处](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)的新 Relic 文档, 获取有关安装 Java agent 和 JBoss 配置的指导。

### <a name="configure-appdynamics"></a>配置 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
2. 从 AppDynamics 网站下载 Java agent, 文件名将类似于*AppServerAgent-x* . x. x. x. x. x. x. x. x. x. x. x。
3. 通过[SSH 连接到应用服务实例](app-service-linux-ssh-support.md), 并创建新的目录 */home/site/wwwroot/apm*。
4. 将 Java 代理文件上传到 */home/site/wwwroot/apm*下的目录中。 代理的文件应在 */home/site/wwwroot/apm/appdynamics*中。
5. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果使用的是**WildFly**, 请参阅[此处](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)的 AppDynamics 文档, 以获取有关安装 Java agent 和 JBoss 配置的指导。

>  如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `-javaagent:/...` 选项追加到当前值的末尾。

## <a name="configure-jar-applications"></a>配置 JAR 应用程序

### <a name="starting-jar-apps"></a>启动 JAR 应用

默认情况下, 应用服务需要 JAR 应用程序的名称为*app.config*。 如果它具有此名称, 它将自动运行。 对于 Maven 用户, 你可以通过`<finalName>app</finalName>` `<build>`在*pom*的部分中包含来设置 JAR 名称。 可以通过设置`archiveFileName`属性[在 Gradle 中执行相同的操作](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)。

如果要将其他名称用于 JAR, 则还必须提供执行 JAR 文件的[启动命令](app-service-linux-faq.md#built-in-images)。 例如， `java -jar my-jar-app.jar` 。 可以在门户中的 "配置" > "常规设置" 下或使用名为`STARTUP_COMMAND`的应用程序设置中设置 "启动" 命令的值。

### <a name="server-port"></a>服务器端口

应用服务 Linux 将传入的请求路由到端口 80, 因此, 应用程序还应在端口80上进行侦听。 你可以在应用程序的配置 (如春季的*application. properties*文件) 中或在启动命令中执行此操作 (例如, `java -jar spring-app.jar --server.port=80`)。 有关常见 Java 框架, 请参阅以下文档:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play 框架](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>数据源

### <a name="tomcat"></a>Tomcat

这些说明适用于所有数据库连接。 你需要使用你选择的数据库的驱动程序类名称和 JAR 文件来填充占位符。 下面提供了一个表，其中包含了常见数据库的类名称和驱动程序下载。

| 数据库   | 驱动程序类名称                             | JDBC 驱动程序                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下载](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下载](https://dev.mysql.com/downloads/connector/j/)（选择“独立于平台”） |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下载](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

若要将 Tomcat 配置为使用 Java Database Connectivity (JDBC) 或 Java 持久性 API (JPA), 请先`CATALINA_OPTS`自定义在启动时由 Tomcat 读入的环境变量。 在[应用服务 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中通过某个应用设置来设置这些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或在 "**配置** > **应用程序设置**" 页的 "Azure 门户中设置环境变量。

接下来，确定数据源应当供一个应用程序使用，还是供在 Tomcat servlet 上运行的所有应用程序使用。

#### <a name="application-level-data-sources"></a>应用程序级数据源

1. 在项目的*元 INF/* 目录中创建*上下文 .xml*文件。 如果*元 INF/* 目录不存在, 请创建它。

2. 在*上下文 .xml*中, 添加`Context`元素以将数据源链接到 JNDI 地址。 将 `driverClassName` 占位符替换为上表中你的驱动程序的类名称。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 更新应用程序的*web.config*以在应用程序中使用数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共享服务器级别资源

1. 如果没有配置, 请使用 SSH 将 */usr/local/tomcat/conf*的内容复制到应用服务 Linux 实例上的 */home/tomcat/conf*中。

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. 在元素中的 `<Server>`元素内添加一个上下文元素。

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. 更新应用程序的*web.config*以在应用程序中使用数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成配置

最后, 将驱动程序 Jar 置于 Tomcat 类路径中, 然后重新启动应用服务。

1. 通过将 JDBC 驱动程序文件放在 */home/tomcat/lib*目录中, 确保该驱动程序文件可用于 Tomcat classloader。 （如果此目录尚未存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：

    1. 在[Cloud Shell](https://shell.azure.com)中, 安装 webapp 扩展:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 运行以下 CLI 命令, 创建从本地系统到应用服务的 SSH 隧道:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 通过 SFTP 客户端连接到本地隧道端口, 并将文件上传到 */home/tomcat/lib*文件夹。

    另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

2. 如果你创建了服务器级数据源，请重启应用服务 Linux 应用程序。 Tomcat 会将 `CATALINA_HOME` 重置为 `/home/tomcat/conf`，并使用更新后的配置。

### <a name="spring-boot"></a>Spring Boot

若要在春季 Boot 应用程序中连接到数据源, 我们建议创建连接字符串并将其注入到*应用程序的 properties*文件中。

1. 在 "应用服务" 页的 "配置" 部分中, 设置字符串的名称, 将 JDBC 连接字符串粘贴到 "值" 字段, 并将 "类型" 设置为 "自定义"。 您可以选择将此连接字符串设置为槽设置。

    应用程序可以访问此连接字符串, 作为名为`CUSTOMCONNSTR_<your-string-name>`的环境变量。 例如, 我们在上面创建的连接字符串将命名为`CUSTOMCONNSTR_exampledb`。

2. 在*应用程序*的 "属性" 文件中, 用环境变量名称引用此连接字符串。 对于我们的示例, 我们将使用以下方法。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

有关本主题的详细信息, 请参阅有关数据访问和[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的[弹簧启动文档](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)。

## <a name="configure-java-ee-wildfly"></a>配置 Java EE (WildFly)

> [!NOTE]
> 应用服务 Linux 上的 Java Enterprise Edition 目前处于预览阶段。 对于面向生产的工作,**不**建议使用此堆栈。 有关 Java SE 和 Tomcat 堆栈的信息。

Linux 上的 Azure App Service 允许 Java 开发人员在完全托管的基于 Linux 的服务上构建、部署和缩放 Java Enterprise (Java EE) 应用程序。  基础 Java 企业运行时环境是开源[WildFly](https://wildfly.org/)应用程序服务器。

本部分包含以下小节：

- [利用应用服务进行缩放](#scale-with-app-service)
- [自定义应用程序服务器配置](#customize-application-server-configuration)
- [安装模块和依赖项](#install-modules-and-dependencies)
- [配置数据源](#configure-data-sources)
- [启用消息传递提供程序](#enable-messaging-providers)
- [配置会话管理缓存](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>应用服务缩放

在 Linux 上的应用服务中运行的 WildFly 应用程序服务器以独立模式运行，而不是在域配置中运行。 横向扩展应用服务计划时，每个 WildFly 实例都被配置为独立的服务器。

使用[缩放规则](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)和[增加实例数](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，纵向或横向缩放应用程序。

### <a name="customize-application-server-configuration"></a>自定义应用程序服务器配置

Web 应用实例是无状态的, 因此在启动时必须配置每个新实例, 以支持应用程序所需的 WildFly 配置。
你可以编写一个用于调用 WildFly CLI 的启动 Bash 脚本，以便执行以下操作：

- 设置数据源
- 配置消息提供程序
- 将其他模块和依赖项添加到 WildFly 服务器配置。

当 WildFly 启动并运行, 但在应用程序启动之前, 该脚本将运行。 该脚本应使用从 */opt/jboss/wildfly/bin/jboss-cli.sh*调用的[JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)来配置应用程序服务器, 以使用服务器启动后所需的任何配置或更改来配置应用程序服务器。

不要使用 CLI 的交互模式来配置 WildFly。 相反，可使用 `--file` 命令向 JBoss CLI 提供命令脚本，例如：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

使用 FTP 将启动脚本上传到 */home*目录下应用服务实例中的某个位置, 例如 */home/site/deployments/tools*。 有关详细信息, 请参阅[使用 FTP/S 将应用部署到 Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

将 Azure 门户中的 "**启动脚本**" 字段设置为启动 shell 脚本的位置, 例如 */home/site/deployments/tools/your-startup-script.sh*。

在应用程序配置中提供应用程序[设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings), 以传递要在脚本中使用的环境变量。 应用程序设置将连接字符串和配置应用程序所需的其他机密置于版本控制之外。

### <a name="install-modules-and-dependencies"></a>安装模块和依赖项

若要通过 JBoss CLI 将模块及其依赖项安装到 WildFly 类路径中, 需要在其自己的目录中创建以下文件。 某些模块和依赖项可能需要其他配置，例如 JNDI 命名或其他特定于 API 的配置，因此此列表是在大多数情况下配置依赖项所需的最小集。

- [XML 模块描述符](https://jboss-modules.github.io/jboss-modules/manual/#descriptors)。 此 XML 文件定义模块的名称、属性和依赖项。 此[示例 module.xml 文件](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)定义了 Postgres 模块、其 JAR 文件 JDBC 依赖项以及所需的其他模块依赖项。
- 模块的任何必要 JAR 文件依赖项。
- 具有 JBoss CLI 命令的脚本，用于配置新模块。 该文件将包含 JBoss CLI 要执行的命令，以配置服务器使用依赖项。 有关添加模块、数据源和消息提供程序的命令的文档，请参阅[本文档](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)。
- 用于调用 JBoss CLI 和执行上一步中脚本的 Bash 启动脚本。 重启应用服务实例或在横向扩展期间配置新实例时，将执行此文件。当 JBoss 命令传递给 JBoss CLI 时，可在此启动脚本中为应用程序执行任何其他配置。 此文件可以是将 JBoss CLI 命令脚本传递给 JBoss CLI 的最小单个命令：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

获取模块的文件和内容后, 请按照以下步骤将模块添加到 WildFly 应用程序服务器。

1. 使用 FTP 将文件上传到 */home*目录下应用服务实例中的某个位置, 例如 */home/site/deployments/tools*。 有关详细信息, 请参阅[使用 FTP/S 将应用部署到 Azure App Service](../deploy-ftp.md)。
2. 在 Azure 门户的 "**配置** > **常规设置**" 页中, 将 "**启动脚本**" 字段设置为启动 shell 脚本的位置, 例如 */home/site/deployments/tools/startup.sh*。
3. 按门户的 "**概述**" 部分中的 "**重新启动**" 按钮或使用 Azure CLI 重启应用服务实例。

### <a name="configure-data-sources"></a>配置数据源

若要配置 WildFly/JBoss 以访问数据源, 请使用上文中的 "安装模块和依赖项" 部分中概述的一般过程。 以下部分提供有关 PostgreSQL、MySQL 和 SQL Server 数据源的此过程的特定详细信息。

本部分假定你已有应用程序、应用服务实例和 Azure 数据库服务实例。 以下说明涉及到你的应用服务名称、其资源组和数据库连接信息。 可以在 Azure 门户中找到此信息。

如果你想要从开始使用示例应用的整个过程开始, 请参阅[教程:在 Azure](tutorial-java-enterprise-postgresql-app.md)中构建 Java EE 和 Postgres web 应用。

以下步骤说明了连接现有应用服务和数据库的要求。

1. 下载适用于[PostgreSQL](https://jdbc.postgresql.org/download.html)、 [MySQL](https://dev.mysql.com/downloads/connector/j/)或[SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)的 JDBC 驱动程序。 解压缩已下载的存档以获取驱动程序 .jar 文件。

2. 使用类似于*module*的名称创建文件, 并添加以下标记。 为 PostgreSQL、 `org.postgres` MySQL`com.mysql`或SQLServer替换占位符(包括尖括号)。`com.microsoft` `<module name>` 将`<JDBC .jar file path>`替换为上一步中的 .jar 文件的名称, 包括将文件放置在应用服务实例中的位置的完整路径。 这可以是 */home*目录下的任何位置。

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. 使用类似于*datasource-commands*的名称创建文件, 并添加以下代码。 替换`<JDBC .jar file path>`为上一步中使用的值。 将`<module file path>`替换为上一步中的文件名和应用服务路径, 例如 */home/module.xml*。

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    此文件由下一步中所述的启动脚本运行。 它将 JDBC 驱动程序安装为 WildFly 模块, 创建相应的 WildFly 数据源, 并重新加载服务器以确保更改生效。

4. 创建名为*startup.sh*的文件, 并添加以下代码。 将`<JBoss CLI script>`替换为在上一步中创建的文件的名称。 请确保在应用服务实例中包含要放置文件的位置的完整路径, 例如 */home/datasource-commands.cli*。

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. 使用 FTP 将 JDBC .jar 文件、模块 XML 文件、JBoss CLI 脚本和启动脚本上传到应用服务实例。 将这些文件放在之前步骤中指定的位置, 例如 */home*。 有关 FTP 的详细信息, 请参阅[使用 ftp/S 将应用部署到 Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

6. 使用 Azure CLI 向应用服务添加保存数据库连接信息的设置。 将`<resource group>` 和`<webapp name>`替换为你的应用服务使用的值。 将`<database server name>` 、`<database name>`、和`<admin password>`替换为你的数据库连接信息。 `<admin name>` 可以从 Azure 门户获取应用服务和数据库信息。

    **PostgreSQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    每个数据库服务器的 DATABASE_CONNECTION_URL 值不同, 而与 Azure 门户上的值不同。 此处所示的 URL 格式 (以及上述代码段中的) 需要使用 WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. 在 Azure 门户中, 导航到应用服务, 并找到 "**配置** > **常规设置**" 页。 将 "**启动脚本**" 字段设置为启动脚本的名称和位置, 例如 */home/startup.sh*。

下次应用服务重新启动时, 它将运行启动脚本并执行必要的配置步骤。 若要测试此配置是否正常发生, 可以使用 SSH 访问应用服务, 然后从 Bash 提示符自行运行启动脚本。 你还可以检查应用服务日志。 有关这些选项的详细信息, 请参阅[日志记录和调试应用](#logging-and-debugging-apps)。

接下来, 需要更新应用的 WildFly 配置并重新部署。 请执行以下步骤：

1. 打开应用的*src/main/resources/META-INF/持久性 .xml*文件, 并找到`<jta-data-source>`元素。 替换其内容, 如下所示:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. 在 Bash 提示符下, 使用以下命令重新生成并重新部署应用:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. 按 Azure 门户的 "**概述**" 部分中的 "**重新启动**" 按钮, 或使用 Azure CLI 来重新启动应用服务实例。

应用服务实例现已配置为可访问数据库。

有关配置 WildFly 的数据库连接的详细信息, 请参阅[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)、 [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)或[SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)。

### <a name="enable-messaging-providers"></a>启用消息传递提供程序

要使用服务总线作为消息传递机制启用消息驱动 Bean，请执行以下操作：

1. 使用 [Apache QPId JMS 消息传递库](https://qpid.apache.org/proton/index.html)。 在应用程序的 pom.xml（或其他生成文件）中包含此依赖项。

2. 创建[服务总线资源](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)。 在该命名空间内创建 Azure 服务总线命名空间和队列，以及具有发送和接收功能的共享访问策略。

3. 通过对策略的主密钥进行 URL 编码或[使用服务总线 SDK ](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)，将共享访问策略密钥传递给代码。

4. 按照安装模块和依赖关系部分中概述的步骤，使用模块 XML 描述符、.jar依赖项、JBoss CLI 命令和 JMS 提供程序的启动脚本。 除这四个文件外，还需要创建一个 XML 文件，该文件定义 JMS 队列和主题的 JNDI 名称。 请参阅[此存储库](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)，获取有关参考配置文件。

### <a name="configure-session-management-caching"></a>配置会话管理缓存

默认情况下，Linux 上的应用服务将使用会话相关性 cookie 来确保将现有会话的客户端请求路由到应用程序的同一实例。 此默认行为不需要配置，但存在一些限制：

- 如果重启或减少应用程序实例，应用程序服务器中的用户会话状态将会丢失。
- 如果应用程序具有较长的会话超时设置或固定数量的用户，则自动调整的新实例可能需要一些时间才能接收负载，因为只有新会话将路由到新启动的实例。

可以将 WildFly 配置为使用外部会话存储, 如[Azure Cache For Redis](/azure/azure-cache-for-redis/)。 需要[禁用现有的 ARR 实例相关性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)配置才能关闭基于会话 cookie 的路由, 并允许已配置的 WildFly 会话存储在不干扰的情况下运行。

## <a name="docker-containers"></a>Docker 容器

若要在容器中使用 Azure 支持的 Zulu JDK，请确保拉取并使用[受支持的 Azul Zulu Enterprise for Azure 下载页面](https://www.azul.com/downloads/azure-only/zulu/)中提到的预构建的映像，或使用 [Microsoft Java GitHub 存储库](https://github.com/Microsoft/java/tree/master/docker)中的 `Dockerfile` 示例。

## <a name="statement-of-support"></a>支持声明

### <a name="runtime-availability"></a>运行时可用性

适用于 Linux 的应用服务支持对 Java Web 应用程序的托管主机使用两个运行时：

- [Tomcat servlet 容器](https://tomcat.apache.org/)：用于运行打包为 Web 存档 (WAR) 文件的应用程序。 支持的版本为 8.5 和 9.0。
- Java SE 运行时环境：用于运行打包为 Java 存档 (JAR) 文件的应用程序。 支持的版本为 Java 8 和11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azul Zulu Enterprise 内部版 OpenJDK 是适用于 Azure 和 Azure Stack 的 OpenJDK 的免费、多平台、生产就绪型发行版，由 Microsoft 及 Azul Systems 提供支持。 这些版本包含构建和运行 Java SE 应用程序所需的所有组件。 可以从[JAVA Jdk 安装](https://aka.ms/azure-jdks)安装 JDK。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

## <a name="next-steps"></a>后续步骤

请访问[面向 Java 开发人员的 Azure](/java/azure/) 中心查找 Azure 快速入门、教程和 Java 参考文档。

[应用服务 Linux 常见问题解答](app-service-linux-faq.md)中解答了并不特定于 Java 开发的、适用于 Linux 的应用服务的一般用法问题。

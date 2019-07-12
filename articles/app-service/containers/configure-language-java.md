---
title: 配置 Linux Java 应用程序-Azure 应用服务 |Microsoft Docs
description: 了解如何配置在 Linux 上的 Azure 应用服务中运行的 Java 应用。
keywords: azure 应用服务、 web 应用、 linux、 os、 java 和 java ee jee，javaee
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
ms.openlocfilehash: af6fd7b99147396a70fccc7b2b11dfef3def15a8
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786298"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>为 Azure 应用服务中配置 Linux Java 应用

Linux 上的 Azure 应用服务可让 Java 开发人员在完全托管的基于 Linux 的服务中快速生成、部署和缩放 Tomcat 或 Java Standard Edition (SE) 打包式 Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供关键概念和 Java 开发人员在应用服务中使用内置的 Linux 容器的说明。 如果你从未使用过 Azure 应用服务，请按照[Java 快速入门](quickstart-java.md)并[Java 与 PostgreSQL 教程](tutorial-java-enterprise-postgresql-app.md)第一个。

## <a name="deploying-your-app"></a>部署应用

可以使用[适用于 Azure 应用服务的 Maven 插件](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)部署.jar 和.war 文件。 使用流行的 Ide 部署也支持使用[用于 IntelliJ 的 Azure 工具包](/java/azure/intellij/azure-toolkit-for-intellij)或[用于 Eclipse 的 Azure 工具包](/java/azure/eclipse/azure-toolkit-for-eclipse)。

否则，你的部署方法将取决于你的存档类型：

- 若要将 .war 文件部署到 Tomcat，请使用 `/api/wardeploy/` 终结点对存档文件执行 POST 操作。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像中的 .jar 文件，请使用 Kudu 站点的 `/api/zipdeploy/` 终结点。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

不要使用 FTP 来部署 .war 或 .jar。 FTP 工具设计用来上传启动脚本、依赖项或其他运行时文件。 它不是用于部署 Web 应用的最佳选项。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 有关详细信息，请参阅[Azure 应用服务诊断概述](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 控制台访问

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>流式传输诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

有关详细信息，请参阅[使用 Azure CLI 流式处理日志](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>应用日志记录

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。 可在 Java 和 Tomcat 应用程序日志 */home/LogFiles/应用程序/* 目录。

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](/azure/application-insights/app-insights-java-trace-logs)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。

### <a name="troubleshooting-tools"></a>故障排除工具

基于内置的 Java 映像[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)操作系统。 使用`apk`包管理器安装任何故障排除工具或命令。

### <a name="flight-recorder"></a>网络流量记录器

应用服务上的所有 Linux Java 映像都已安装，以便可以轻松地连接到 JVM 并启动探查器录制或生成的堆转储的 Zulu 网络流量记录器。

#### <a name="timed-recording"></a>定时的录制

若要获取体验，通过 ssh 登录到你的应用服务并运行`jcmd`命令以查看所有运行的 Java 进程的列表。 除了本身的 jcmd，应会看到 Java 应用程序运行带有进程 ID 号 (pid)。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

执行以下命令以启动 JVM 的 30 秒录制。 这将 JVM 配置文件，并创建一个名为 JFR 文件*jfr_example.jfr*主目录中。 （116 将替换为你的 Java 应用的 pid。）

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在 30 的第二个间隔内，可以验证记录通过运行正在进行`jcmd 116 JFR.check`。 这将显示对给定的 Java 进程的所有记录。

#### <a name="continuous-recording"></a>连续录制

可以使用 Zulu 网络流量记录器持续分析对运行时性能的影响最小 Java 应用程序 ([源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf))。 为此，请运行以下 Azure CLI 命令，以创建所需的配置创建名为 JAVA_OPTS 应用设置。 JAVA_OPTS 应用设置的内容传递给`java`命令启动您的应用程序时。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

有关详细信息，请参阅[Jcmd 命令参考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析录制

使用[FTPS](../deploy-ftp.md) JFR 文件下载到本地计算机。 若要分析 JFR 文件，下载并安装[Zulu 的任务控制](https://www.azul.com/products/zulu-mission-control/)。 Zulu 的任务控制的说明，请参阅[Azul 文档](https://docs.azul.com/zmc/)并[安装说明](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自定义和优化

适用于 Linux 的 azure 应用服务支持带框优化并通过 Azure 门户和 CLI 进行自定义。 查看非特定于 Java 的 web 应用配置的以下文章：

- [配置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [设置自定义域](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [启用 SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [添加 CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [配置 Kudu 站点](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要设置已分配的内存或其他 JVM 运行时选项在 Tomcat 和 Java SE 环境中，创建[应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)名为`JAVA_OPTS`的选项。 应用服务 Linux 在启动时，会将此设置作为环境变量传递给 Java 运行时。

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含其他设置的新应用设置，例如 `-Xms512m -Xmx1204m`。 

若要配置的 Maven 插件中设置应用设置，请在 Azure 插件部分中添加设置/值标记。 下面的示例设置特定最小值和最大 Java 堆大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

在应用服务计划中运行包含一个部署槽位的单个应用程序的开发人员可以使用以下选项：

- B1 和 S1 实例： `-Xms1024m -Xmx1024m`
- B2 和 S2 的实例： `-Xms3072m -Xmx3072m`
- B3 和 S3 的实例： `-Xms6144m -Xmx6144m`

优化应用程序堆设置时，请查看应用服务计划详细信息，并考虑多个应用程序和部署槽位方面的需求，以得出最佳内存分配。

如果要部署的 JAR 应用程序，它应命名*app.jar*以便内置映像可以正确地标识您的应用程序。 （的 Maven 插件情况执行此重命名自动）。如果不希望重命名为 JAR *app.jar*，可以上传包含要运行 JAR 的命令的 shell 脚本。 然后粘贴到此脚本中的完整路径[启动文件](app-service-linux-faq.md#built-in-images)在门户的配置部分中的文本框。

### <a name="turn-on-web-sockets"></a>启用 Web 套接字

在 Azure 门户中应用程序的“应用程序设置”中启用 Web 套接字支持。  需要重启应用程序才能使设置生效。

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

如果你的 Java 应用程序是特别大，则应增加启动时间限制。 若要执行此操作，创建应用程序设置，`WEBSITES_CONTAINER_START_TIME_LIMIT`并将其设置为应用服务超时前应等待的秒数。最大值是`1800`秒。

### <a name="pre-compile-jsp-files"></a>预编译 JSP 文件

若要提高 Tomcat 应用程序的性能，可以部署到应用服务之前编译 JSP 文件。 可以使用[的 Maven 插件](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)提供 Apache 投掷，或使用这[Ant 生成文件](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>安全应用程序

在适用于 Linux 应用服务中运行的 Java 应用程序实施与其他应用程序相同的一套[安全最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users"></a>对用户进行身份验证

设置应用程序在 Azure 门户中使用的身份验证**身份验证和授权**选项。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。 有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以及其他标识提供者的相关文章。 如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)一文中的说明。

#### <a name="tomcat"></a>Tomcat

Tomcat 应用程序可以访问用户的声明直接从 Tomcat servlet 通过转换主体对象复制到 Map 对象。 Map 对象将每个声明类型映射到该类型声明的集合。 在下面的代码，`request`的一个实例`HttpServletRequest`。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

现在可以检查`Map`任何特定的声明的对象。 例如，以下代码片段循环访问所有声明类型，并输出每个集合的内容。

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

若要注销用户，并执行其他操作，请参阅的文档[应用服务身份验证和授权的使用情况](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)。 此外，还有 Tomcat 上的官方文档[HttpServletRequest 接口](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法。 以下方法也都会冻结的 servlet 基于你的应用服务配置：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要禁用此功能，创建名为应用程序设置`WEBSITE_AUTH_SKIP_PRINCIPAL`值为`1`。 若要禁用所有 servlet 筛选器添加的应用服务，创建名为的设置`WEBSITE_SKIP_FILTERS`值为`1`。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 开发人员可以使用 [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) 通过熟悉的 Spring Security 注释和 API 来保护应用程序。 增加最大标头大小，以确保你*application.properties*文件。 我们建议值为 `16384`。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

遵照[绑定现有的自定义 SSL 证书](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)中的说明上传现有的 SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用密钥保管库的引用

[Azure 密钥保管库](../../key-vault/key-vault-overview.md)提供与访问策略和审核历史记录的集中式密钥管理。 可以在密钥保管库中存储机密 （如密码或连接字符串），并通过环境变量在应用程序中访问这些机密。

首先，按照说明[授予应用访问 Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)并[在应用程序设置中进行对你的密码的密钥保管库引用](../app-service-key-vault-references.md#reference-syntax)。 您可以验证解析为机密的引用的远程访问应用服务终端时打印环境变量。

若要将注入 Spring 或 Tomcat 配置文件中的这些机密，使用环境变量注入语法 (`${MY_ENV_VAR}`)。 对于 Spring 配置文件，请参阅本文档中有关[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)。

## <a name="configure-apm-platforms"></a>配置 APM 的平台

本部分介绍如何连接 NewRelic 和 AppDynamics 应用程序性能监视 (APM) 平台与部署在 Linux 上的 Azure 应用服务上的 Java 应用程序。

[配置 New Relic](#configure-new-relic)
[配置 AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>配置 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 NewRelic 帐户
2. 从 NewRelic 下载 Java 代理，它将拥有的文件名称类似于*newrelic java x.x.x.zip*。
3. 复制你的许可证密钥，稍后需要使用它来配置代理。
4. [通过 ssh 登录到应用服务实例](app-service-linux-ssh-support.md)并创建一个新目录 */home/site/wwwroot/apm*。
5. 将解压缩的 NewRelic Java 代理文件上传到下的目录 */home/site/wwwroot/apm*。 你的代理的文件应采用 */home/site/wwwroot/apm/newrelic*。
6. 修改在 YAML 文件 */home/site/wwwroot/apm/newrelic/newrelic.yml*并将占位符许可证值替换为你自己的许可证密钥。
7. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你的应用使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果您使用的**WildFly**，请参阅 New Relic 文档[此处](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)有关安装 Java 代理和 JBoss 配置的指南。
    - 如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `javaagent` 选项追加到当前值的末尾。

### <a name="configure-appdynamics"></a>配置 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
2. 从 AppDynamics 网站下载 Java 代理、 文件名称将类似于*AppServerAgent x.x.x.xxxxx.zip*
3. [通过 ssh 登录到应用服务实例](app-service-linux-ssh-support.md)并创建一个新目录 */home/site/wwwroot/apm*。
4. 将 Java 代理文件上传到下的目录 */home/site/wwwroot/apm*。 你的代理的文件应采用 */home/site/wwwroot/apm/appdynamics*。
5. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果您使用的**WildFly**，请参阅 AppDynamics 文档[此处](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)有关安装 Java 代理和 JBoss 配置的指南。

## <a name="configure-jar-applications"></a>配置应用程序 JAR

### <a name="starting-jar-apps"></a>启动 JAR 应用

默认情况下，应用服务需要你 JAR 的应用程序命名为*app.jar*。 如果它具有此名称，将自动运行。 对于 Maven 用户中，您可以设置的 JAR 名称通过包括`<finalName>app</finalName>`中`<build>`一部分您*pom.xml*。 [您可以在 Gradle 这样做](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)通过设置`archiveFileName`属性。

如果你想要使用不同的 JAR 名称，还必须提供[启动命令](app-service-linux-faq.md#built-in-images)执行 JAR 文件。 例如， `java -jar my-jar-app.jar` 。 可以为您在门户中，在配置下的启动命令设置值 > 常规设置，或使用名为应用程序设置`STARTUP_COMMAND`。

### <a name="server-port"></a>服务器端口

Linux 版应用服务将传入请求路由到端口 80，以便你的应用程序应侦听端口 80 也。 您可以执行此操作在应用程序配置 (如 Spring 的*application.properties*文件)，或在启动命令 (例如， `java -jar spring-app.jar --server.port=80`)。 请参阅以下文档为常见的 Java 框架：

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
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

若要配置为使用 Java 数据库连接 (JDBC) 或 Java 持久性 API (JPA) 的 Tomcat，首先要自定义`CATALINA_OPTS`中阅读，tomcat 在启动时的环境变量。 在[应用服务 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中通过某个应用设置来设置这些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或中设置环境变量**配置** > **应用程序设置**在 Azure 门户中的页。

接下来，确定数据源应当供一个应用程序使用，还是供在 Tomcat servlet 上运行的所有应用程序使用。

#### <a name="application-level-data-sources"></a>应用程序级别的数据源

1. 创建*context.xml*中的文件*元 INF /* 项目的目录。 创建*META INF /* 目录，如果不存在。

2. 在中*context.xml*，添加`Context`元素的数据源链接到 JNDI 地址。 将 `driverClassName` 占位符替换为上表中你的驱动程序的类名称。

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

3. 更新应用程序*web.xml*使用你的应用程序中的数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>服务器级别的共享的资源

1. 将复制的内容 */usr/local/tomcat/conf*成 */home/tomcat/conf*实例在应用服务 Linux 上的如果您没有配置那里已使用 SSH。

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. 将上下文元素中的添加你*server.xml*内`<Server>`元素。

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

3. 更新应用程序*web.xml*使用你的应用程序中的数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成配置

最后，将驱动程序 jar 文件放在 Tomcat classpath 并重新启动你的应用服务。

1. 确保 JDBC 驱动程序文件，从而将它们中都可用于 Tomcat 引发的 classloader */home/tomcat/lib*目录。 （如果此目录尚未存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：

    1. 在中[Cloud Shell](https://shell.azure.com)，安装 web 应用扩展：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 运行以下 CLI 命令来创建 SSH 隧道通过本地系统到应用服务：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 连接到使用 SFTP 客户端的本地隧道端口并将文件上载到 */home/tomcat/lib*文件夹。

    另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

2. 如果你创建了服务器级数据源，请重启应用服务 Linux 应用程序。 Tomcat 会将 `CATALINA_HOME` 重置为 `/home/tomcat/conf`，并使用更新后的配置。

### <a name="spring-boot"></a>Spring Boot

若要连接到 Spring Boot 应用程序中的数据源，我们建议创建的连接字符串并将其注入您*application.properties*文件。

1. 在应用服务页的"配置"部分，设置字符串的名称将 JDBC 连接字符串粘贴到值字段中，而设置为"Custom"的类型。 槽设置为，可以选择性地设置此连接字符串。

    此连接字符串都可以访问我们的应用程序为环境变量名为`CUSTOMCONNSTR_<your-string-name>`。 例如，我们在上面创建的连接字符串将被命名为`CUSTOMCONNSTR_exampledb`。

2. 在你*application.properties*文件中，引用环境变量名称与此连接字符串。 对于我们的示例，我们将使用以下命令。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

请参阅[Spring Boot 文档数据的访问权限](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)并[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)有关本主题的详细信息。

## <a name="configure-java-ee-wildfly"></a>配置 Java EE (WildFly)

> [!NOTE]
> 应用服务 Linux 上的 Java 企业版目前处于预览状态。 此堆栈**不**建议用于面向生产的工作。 我们 Java SE 和 Tomcat 在堆栈上的信息。

Linux 上的 azure 应用服务允许 Java 开发人员能够生成、 部署和缩放 Java 企业 (Java EE) 应用程序上完全托管的基于 Linux 的服务。  基础 Java 企业运行时环境是一个开放源代码[WildFly](https://wildfly.org/)应用程序服务器。

本部分包含以下小节：

- [使用应用服务进行缩放](#scale-with-app-service)
- [自定义应用程序服务器配置](#customize-application-server-configuration)
- [安装模块依赖项](#install-modules-and-dependencies)
- [配置数据源](#configure-data-sources)
- [启用消息传递提供程序](#enable-messaging-providers)
- [配置会话管理缓存](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>应用服务缩放

在 Linux 上的应用服务中运行的 WildFly 应用程序服务器以独立模式运行，而不是在域配置中运行。 横向扩展应用服务计划时，每个 WildFly 实例都被配置为独立的服务器。

使用[缩放规则](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)和[增加实例数](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，纵向或横向缩放应用程序。

### <a name="customize-application-server-configuration"></a>自定义应用程序服务器配置

Web 应用程序实例是无状态，，因此在启动时以支持应用程序所需的 WildFly 配置，必须配置每个新的实例已启动。
你可以编写一个用于调用 WildFly CLI 的启动 Bash 脚本，以便执行以下操作：

- 设置数据源
- 配置消息提供程序
- 将其他模块和依赖项添加到 WildFly 服务器配置。

该脚本运行时 WildFly 已启动并运行，但应用程序启动之前。 该脚本应使用[JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)从调用 */opt/jboss/wildfly/bin/jboss-cli.sh*若要配置的任何配置或需要在服务器启动后更改应用程序服务器。

不使用 CLI 交互模式下配置 WildFly。 相反，可使用 `--file` 命令向 JBoss CLI 提供命令脚本，例如：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

使用 FTP 将启动脚本上传到在应用服务实例中的某个位置你 */home*目录，如 */home/site/deployments/tools*。 有关详细信息，请参阅[将应用部署到 Azure 应用服务中使用 FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

设置**启动脚本**在 Azure 门户中的位置启动 shell 脚本，例如字段 */home/site/deployments/tools/your-startup-script.sh*。

提供[应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)中要将环境变量用于传递脚本中的应用程序配置。 应用程序设置将连接字符串和配置应用程序所需的其他机密置于版本控制之外。

### <a name="install-modules-and-dependencies"></a>安装模块依赖项

若要到通过 JBoss CLI WildFly 类路径中安装模块及其依赖项，将需要在其自己的目录中创建以下文件。 某些模块和依赖项可能需要其他配置，例如 JNDI 命名或其他特定于 API 的配置，因此此列表是在大多数情况下配置依赖项所需的最小集。

- [XML 模块描述符](https://jboss-modules.github.io/jboss-modules/manual/#descriptors)。 此 XML 文件定义模块的名称、属性和依赖项。 此[示例 module.xml 文件](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)定义了 Postgres 模块、其 JAR 文件 JDBC 依赖项以及所需的其他模块依赖项。
- 模块的任何必要 JAR 文件依赖项。
- 具有 JBoss CLI 命令的脚本，用于配置新模块。 该文件将包含 JBoss CLI 要执行的命令，以配置服务器使用依赖项。 有关添加模块、数据源和消息提供程序的命令的文档，请参阅[本文档](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)。
- 用于调用 JBoss CLI 和执行上一步中脚本的 Bash 启动脚本。 重启应用服务实例或在横向扩展期间配置新实例时，将执行此文件。当 JBoss 命令传递给 JBoss CLI 时，可在此启动脚本中为应用程序执行任何其他配置。 此文件可以是将 JBoss CLI 命令脚本传递给 JBoss CLI 的最小单个命令：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

后的文件和内容为模块，请执行以下步骤来将模块添加到 WildFly 应用程序服务器。

1. 使用 FTP 将文件上传到在应用服务实例中的位置在 */home*目录，如 */home/site/deployments/tools*。 有关详细信息，请参阅[将应用部署到 Azure 应用服务中使用 FTP/S](../deploy-ftp.md)。
2. 在**配置** > **常规设置**页上的 Azure 门户中，设置**启动脚本**到启动 shell 脚本，位置字段示例 */home/site/deployments/tools/startup.sh*。
3. 按下重新启动您的应用服务实例**重新启动**按钮**概述**门户或使用 Azure CLI 的部分。

### <a name="configure-data-sources"></a>配置数据源

若要配置 WildFly/JBoss，若要访问的数据源，您可以使用上述的"安装模块和依赖项"部分中的一般过程。 以下部分提供此过程对于 PostgreSQL、 MySQL 和 SQL Server 数据源的特定详细信息。

本部分假设你已有一个应用、 应用服务实例和 Azure 数据库服务实例。 下面的说明，请参阅你的应用服务名称、 其资源组，和你的数据库连接信息。 您可以找到此信息在 Azure 门户上。

如果想要从开始使用示例应用会经历整个过程，请参阅[教程：构建在 Azure 中的 web 应用 Java EE 和 Postgres](tutorial-java-enterprise-postgresql-app.md)。

以下步骤介绍连接现有的应用服务和数据库的要求。

1. 下载的 JDBC driver for [PostgreSQL](https://jdbc.postgresql.org/download.html)， [MySQL](https://dev.mysql.com/downloads/connector/j/)，或[SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。 解压缩已下载的存档以获取.jar 文件的驱动程序。

2. 创建文件名称类似于*module.xml*并添加以下标记。 替换`<module name>`占位符 （包括在尖括号内） 替换`org.postgres`PostgreSQL，对于`com.mysql`MySQL 的或`com.microsoft`适用于 SQL Server。 替换为`<JDBC .jar file path>`与上一步的.jar 文件的名称，包括位置的完整路径将将文件放在应用服务实例中。 这可以是下的任何位置 */home*目录。

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

3. 创建文件名称类似于*数据源 commands.cli*并添加以下代码。 替换为`<JDBC .jar file path>`与上一步中使用的值。 替换`<module file path>`使用的文件名称和从上一步骤中，例如应用服务路径 */home/module.xml*。

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

    运行此文件，则通过在下一步中所述的启动脚本。 它将作为 WildFly 模块安装 JDBC 驱动程序、 创建相应的 WildFly 数据源，然后重新加载服务器以确保所做的更改将生效。

4. 创建文件名称类似于*startup.sh*并添加以下代码。 替换为`<JBoss CLI script>`与上一步中创建的文件的名称。 请务必包括完整路径到的位置，将文件置于您的应用服务实例，例如 */home/datasource-commands.cli*。

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. 使用 FTP 将 JDBC.jar 文件、 模块 XML 文件、 JBoss CLI 脚本和启动脚本上传到应用服务实例。 将这些文件放入在上一步骤中，如指定的位置 */home*。 有关 FTP 的详细信息，请参阅[将应用部署到 Azure 应用服务中使用 FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

6. 使用 Azure CLI 将设置添加到你保存你的数据库连接信息的应用服务。 替换`<resource group>`和`<webapp name>`你的应用服务使用的值。 替换`<database server name>`， `<database name>`， `<admin name>`，和`<admin password>`与你的数据库连接信息。 可以从 Azure 门户来获取你的应用服务和数据库信息。

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

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

    DATABASE_CONNECTION_URL 值为不同的每个数据库服务器和不同于在 Azure 门户中的值。 所示 （在此处以及上面的代码段） 的 URL 格式所需的使用由 WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. 在 Azure 门户中，导航到你的应用服务，并查找**配置** > **常规设置**页。 设置**启动脚本**字段的名称和位置的启动脚本，例如 */home/startup.sh*。

下一次重新启动你的应用服务，它将运行启动脚本，并执行必要的配置步骤。 若要测试此配置中正确进行，你可以访问使用 SSH 对应用服务，然后运行启动脚本自己从在 Bash 提示符处。 您还可以检查应用服务日志。 有关这些选项的详细信息，请参阅[日志记录和调试应用](#logging-and-debugging-apps)。

接下来，需要更新您的应用程序的 WildFly 配置和重新部署它。 请执行以下步骤：

1. 打开*src/main/resources/META-INF/persistence.xml*适用于应用和查找文件`<jta-data-source>`元素。 将其内容为如下所示：

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

2. 重新生成并重新部署应用程序在 Bash 提示符处使用以下命令：

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. 按下重新启动您的应用服务实例**重新启动**按钮**概述**部分在 Azure 门户或通过使用 Azure CLI。

现在，您的应用服务实例已配置为访问您的数据库。

有关使用 WildFly 配置数据库连接的详细信息，请参阅[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)， [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)，或[SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)。

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

你可以配置 WildFly 若要使用如下所示的外部会话存储[适用于 Redis 的 Azure 缓存](/azure/azure-cache-for-redis/)。 你将需要[禁用现有 ARR 实例关联](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)配置，以关闭会话基于 cookie 的路由，并允许已配置的 WildFly 会话存储不受干扰地运行。

## <a name="docker-containers"></a>Docker 容器

若要在容器中使用 Azure 支持的 Zulu JDK，请确保拉取并使用[受支持的 Azul Zulu Enterprise for Azure 下载页面](https://www.azul.com/downloads/azure-only/zulu/)中提到的预构建的映像，或使用 [Microsoft Java GitHub 存储库](https://github.com/Microsoft/java/tree/master/docker)中的 `Dockerfile` 示例。

## <a name="statement-of-support"></a>支持声明

### <a name="runtime-availability"></a>运行时可用性

适用于 Linux 的应用服务支持对 Java Web 应用程序的托管主机使用两个运行时：

- [Tomcat servlet 容器](https://tomcat.apache.org/)：用于运行打包为 Web 存档 (WAR) 文件的应用程序。 支持的版本为 8.5 和 9.0。
- Java SE 运行时环境：用于运行打包为 Java 存档 (JAR) 文件的应用程序。 支持的版本为 Java 8 和 11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azul Zulu Enterprise 内部版 OpenJDK 是适用于 Azure 和 Azure Stack 的 OpenJDK 的免费、多平台、生产就绪型发行版，由 Microsoft 及 Azul Systems 提供支持。 这些版本包含构建和运行 Java SE 应用程序所需的所有组件。 你可以安装中的 JDK [Java JDK 安装](https://aka.ms/azure-jdks)。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

## <a name="next-steps"></a>后续步骤

请访问[面向 Java 开发人员的 Azure](/java/azure/) 中心查找 Azure 快速入门、教程和 Java 参考文档。

[应用服务 Linux 常见问题解答](app-service-linux-faq.md)中解答了并不特定于 Java 开发的、适用于 Linux 的应用服务的一般用法问题。

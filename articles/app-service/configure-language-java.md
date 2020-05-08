---
title: 配置 Windows Java 应用
description: 了解如何将 Java 应用配置为在 Azure 应用服务中的 Windows VM 实例上运行。 本文介绍最常见的配置任务。
keywords: azure 应用服务, web 应用, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e42096e7ab950e5d8046ec6140c01b24643cb87
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891467"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>为 Azure 应用服务配置 Windows Java 应用

Azure 应用服务可让 Java 开发人员在完全托管的基于 Windows 的服务中快速生成、部署和缩放其 Tomcat Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供了 Java 开发人员可在应用服务中使用的重要概念和说明。 如果你从未用过 Azure 应用服务，首先应该通读 [Java 快速入门](app-service-web-get-started-java.md)。 [应用服务 Windows 常见问题解答](faq-configuration-and-management.md)中解答了有关使用应用服务且非特定于 Java 开发的一般问题。

## <a name="deploying-your-app"></a>部署应用

可以使用[适用于 Maven 的 Azure Web 应用插件](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)来部署 .war 文件。 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) 或 [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse) 还支持通过流行的 IDE 进行部署。

如果不使用这些方法，则部署方法将取决于存档类型：

- 若要将 .war 文件部署到 Tomcat，请使用 `/api/wardeploy/` 终结点对存档文件执行 POST 操作。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要将 .jar 文件部署到 Java SE，请使用 Kudu 站点的 `/api/zipdeploy/` 终结点。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

不要使用 FTP 来部署 .war。 FTP 工具设计用来上传启动脚本、依赖项或其他运行时文件。 它不是用于部署 Web 应用的最佳选项。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 有关详细信息，请参阅[Azure App Service 诊断概述](overview-diagnostics.md)。

### <a name="use-flight-recorder"></a>使用网络流量记录器

应用服务上使用 Azul Jvm 的所有 Java 运行时均随附于祖鲁网络流量记录器。 您可以使用此记录来记录 JVM、系统和 Java 级别事件，以监视 Java 应用程序中的行为和解决问题。

要拍摄定时记录，需要 Java 应用程序的 PID （进程 ID）。 若要查找 PID，请在 https://中打开 web 应用的 SCM 站点的浏览器 <> 你的 scm.azurewebsites.net/ProcessExplorer/。 此页面显示 web 应用中正在运行的进程。 在表中找到名为 "java" 的进程，并复制相应的 PID （进程 ID）。

接下来，打开 SCM 网站顶部工具栏中的**调试控制台**，并运行以下命令。 将`<pid>`替换为前面复制的进程 ID。 此命令将启动 Java 应用程序的30秒探查器记录，并在`timed_recording_example.jfr` `D:\home`目录中生成名为的文件。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

有关详细信息，请参阅[Jcmd 命令参考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

#### <a name="analyze-jfr-files"></a>分析`.jfr`文件

使用[FTPS](deploy-ftp.md)将 JFR 文件下载到本地计算机。 若要分析 JFR 文件，请下载并安装[祖鲁任务控制](https://www.azul.com/products/zulu-mission-control/)。 有关祖鲁任务控制的说明，请参阅[Azul 文档](https://docs.azul.com/zmc/)和[安装说明](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

### <a name="stream-diagnostic-logs"></a>流式传输诊断日志

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

有关详细信息，请参阅[Cloud Shell 中的流日志](troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>应用日志记录

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。 可以在 */LogFiles/Application/* 目录中找到 Java 和 Tomcat 应用日志。

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](/azure/application-insights/app-insights-java-trace-logs)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。


## <a name="customization-and-tuning"></a>自定义和优化

Azure 应用服务原生支持通过 Azure 门户和 CLI 进行优化和自定义。 请查看以下文章了解非特定于 Java 的 Web 应用配置：

- [配置应用设置](configure-common.md#configure-app-settings)
- [设置自定义域](app-service-web-tutorial-custom-domain.md)
- [配置 TLS 绑定](configure-ssl-bindings.md)
- [添加 CDN](../cdn/cdn-add-to-web-app.md)
- [配置 Kudu 站点](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要设置分配的内存或其他 JVM 运行时选项，请使用这些选项创建名为 `JAVA_OPTS` 的[应用设置](configure-common.md#configure-app-settings)。 应用服务在启动时，会将此设置作为环境变量传递给 Java 运行时。

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含其他设置的新应用设置，例如 `-Xms512m -Xmx1204m`。****

若要通过 Maven 插件配置应用设置，请在 Azure 插件部分中添加设置/值标记。 以下示例设置特定的最小和最大 Java 堆大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

在应用服务计划中运行包含一个部署槽位的单个应用程序的开发人员可以使用以下选项：

- B1 和 S1 实例：`-Xms1024m -Xmx1024m`
- B2 和 S2 实例：`-Xms3072m -Xmx3072m`
- B3 和 S3 实例：`-Xms6144m -Xmx6144m`

优化应用程序堆设置时，请查看应用服务计划详细信息，并考虑多个应用程序和部署槽位方面的需求，以得出最佳内存分配。

### <a name="turn-on-web-sockets"></a>启用 Web 套接字

在 Azure 门户中应用程序的“应用程序设置”中启用 Web 套接字支持。**** 需要重启应用程序才能使设置生效。

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

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含值 `-Dfile.encoding=UTF-8` 的新应用设置。****

或者，可以使用应用服务 Maven 插件配置应用设置。 在插件配置中添加设置名称和值标记：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>预编译 JSP 文件

若要提高 Tomcat 应用程序的性能，可以在部署到应用服务之前编译 JSP 文件。 可以使用 Apache Sling 提供的 [Maven 插件](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，或使用此 [Ant 生成文件](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>安全应用程序

在应用服务中运行的 Java 应用程序与其他应用程序具有相同的一组[安全最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users-easy-auth"></a>对用户进行身份验证（简易身份验证）

在 Azure 门户中设置采用**身份验证和授权**选项的应用身份验证。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。 有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](configure-authentication-provider-aad.md)，以及其他标识提供者的相关文章。 如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](app-service-authentication-how-to.md)一文中的说明。

#### <a name="tomcat"></a>Tomcat

Tomcat 应用程序可以通过将主体对象强制转换为 Map 对象，直接从 servlet 访问用户的声明。 该 Map 对象将每个声明类型映射到该类型的声明集合。 在以下代码中，`request` 是 `HttpServletRequest` 的实例。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

现在，可以在 `Map` 对象中检查任何特定声明。 例如，以下代码片段将循环访问所有声明类型，并输出每个集合的内容。

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

若要将用户注销，请使用 `/.auth/ext/logout` 路径。 若要执行其他操作，请参阅有关[应用服务身份验证和授权的用法](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)文档。 Tomcat [HttpServletRequest 接口](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法也有相关的官方文档。 以下 servlet 方法也是基于应用服务配置合成的：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要禁用此功能，请创建名为 `WEBSITE_AUTH_SKIP_PRINCIPAL`、值为 `1` 的应用程序设置。 若要禁用应用服务添加的所有 servlet 筛选器，请创建名为 `WEBSITE_SKIP_FILTERS`、值为 `1` 的设置。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

按照 Azure App Service 中的 "[保护自定义 DNS 名称](configure-ssl-bindings.md)" 中的说明操作，上传现有的 TLS/SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 引用

[Azure KeyVault](../key-vault/general/overview.md) 使用访问策略和审核历史记录来提供集中式机密管理。 可以在 KeyVault 中存储机密（例如密码或连接字符串），然后通过环境变量在应用程序中访问这些机密。

首先，按照有关[为应用授予对 Key Vault 的访问权限](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)以及[在应用程序设置中添加对机密的 KeyVault 引用](app-service-key-vault-references.md#reference-syntax)的说明操作。 可以在远程访问应用服务终端时，通过输出环境变量来验证该引用是否解析为机密。

若要在 Spring 或 Tomcat 配置文件中注入这些机密，请使用环境变量注入语法 (`${MY_ENV_VAR}`)。 有关 Spring 配置文件，请参阅这篇有关[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的文档。


## <a name="configure-apm-platforms"></a>配置 APM 平台

本部分介绍如何使用 NewRelic 和 AppDynamics 应用程序性能监视 (APM) 平台连接 Linux 上的 Azure 应用服务中部署的 Java 应用程序。

### <a name="configure-new-relic"></a>配置 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 New Relic 帐户
2. 从 NewRelic 下载 Java 代理，其文件名类似于 *newrelic-java-x.x.x.zip*。
3. 复制你的许可证密钥，稍后需要使用它来配置代理。
4. 使用 [Kudu 控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)创建新目录 */home/site/wwwroot/apm*。
5. 将解压缩后的 New Relic Java 代理文件上传到 */home/site/wwwroot/apm* 下的某个目录中。 代理文件应位于 */home/site/wwwroot/apm/newrelic* 中。
6. 修改 */home/site/wwwroot/apm/newrelic/newrelic.yml* 中的 YAML 文件，并将占位符许可证值替换为自己的许可密钥。
7. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你的应用使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。

### <a name="configure-appdynamics"></a>配置 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
2. 从 AppDynamics 网站下载 Java 代理，其文件名类似于 *AppServerAgent-x.x.x.xxxxx.zip*
3. 使用 [Kudu 控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)创建新目录 */home/site/wwwroot/apm*。
4. 将 Java 代理文件上传到 */home/site/wwwroot/apm* 下的某个目录中。 代理文件应位于 */home/site/wwwroot/apm/appdynamics* 中。
5. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。

>  如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `-javaagent:/...` 选项追加到当前值的末尾。

## <a name="data-sources"></a>数据源

### <a name="tomcat"></a>Tomcat

这些说明适用于所有数据库连接。 你需要使用你选择的数据库的驱动程序类名称和 JAR 文件来填充占位符。 下面提供了一个表，其中包含了常见数据库的类名称和驱动程序下载。

| 数据库   | 驱动程序类名称                             | JDBC 驱动程序                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下载](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下载](https://dev.mysql.com/downloads/connector/j/)（选择“独立于平台”） |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下载](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

若要将 Tomcat 配置为使用 Java 数据库连接 (JDBC) 或 Java 持久性 API (JPA)，请先自定义在启动时由 Tomcat 读取的 `CATALINA_OPTS` 环境变量。 在[应用服务 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中通过某个应用设置来设置这些值：

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

1. 在项目的 *META-INF/* 目录中创建一个 *context.xml* 文件。 如果 *META-INF/* 目录不存在，请创建它。

2. 在 *context.xml* 中，添加一个 `Context` 元素以将数据源链接到 JNDI 地址。 将 `driverClassName` 占位符替换为上表中你的驱动程序的类名称。

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

3. 更新应用程序的 *web.xml*，以便在应用程序中使用该数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成配置

最后，将驱动程序 JAR 放在 Tomcat 类路径中并重启应用服务。 将 JDBC 驱动程序文件放入 */home/tomcat/lib* 目录，确保它们可供 Tomcat 类加载器使用。 （如果此目录尚不存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：

1. 在[Cloud Shell](https://shell.azure.com)中，安装 webapp 扩展：

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 运行以下 CLI 命令，创建从本地系统到应用服务的 SSH 隧道：

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. 使用 SFTP 客户端连接到本地隧道端口，并将文件上传到 */home/tomcat/lib* 文件夹中。

另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](deploy-configure-credentials.md)。

## <a name="configuring-tomcat"></a>配置 Tomcat

若要编辑 Tomcat 的 `server.xml` 或其他配置文件，请首先记下门户中你的 Tomcat 主版本。

1. 通过运行 `env` 命令查找你的版本的 Tomcat 主目录。 搜索以 `AZURE_TOMCAT` 开头并且与你的主版本匹配的环境变量。 例如，`AZURE_TOMCAT85_HOME` 指向 Tomcat 8.5 的 Tomcat 目录。
1. 查明你的版本的 Tomcat 主目录后，将配置目录复制到 `D:\home`。 例如，如果 `AZURE_TOMCAT85_HOME` 的值为 `D:\Program Files (x86)\apache-tomcat-8.5.37`，则所复制目录的新路径将为 `D:\home\apache-tomcat-8.5.37`。

最后，请重启应用服务。 你的部署应当转到 `D:\home\site\wwwroot\webapps`，跟以前完全一样。

## <a name="configure-java-se"></a>配置 Java SE

在运行时。Windows `server.port`上 Java SE 上的 JAR 应用程序在应用程序启动时作为命令行选项传递。 可以从环境变量手动解析 HTTP 端口`HTTP_PLATFORM_PORT`。 此环境变量的值将是应用程序应侦听的 HTTP 端口。 

## <a name="java-runtime-statement-of-support"></a>Java 运行时支持声明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azure 支持的 Java 开发工具包 (JDK) 为提供 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主版本更新将通过适用于 Windows 的 Azure 应用服务中的新运行时选项提供。 客户可以通过配置应用服务部署来更新到这些较新的 Java 版本，他们需要负责测试和确保重大更新符合其需求。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。 有关 Azure 上的 Java 的详细信息，请参阅[此支持文档](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

Tomcat 8.0 已[于2018年9月30日结束](https://tomcat.apache.org/tomcat-80-eol.html)。 尽管运行时仍在 Azure App Service 上在，但 Azure 不会对 Tomcat 8.0 应用安全更新。 如果可能，请将应用程序迁移到 Tomcat 8.5 或9.0。 Azure App Service 上提供 Tomcat 8.5 和9.0。 有关详细信息，请参阅[官方 Tomcat 站点](https://tomcat.apache.org/whichversion.html)。 

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

### <a name="local-development"></a>本地开发

开发人员可以从 [Azul 下载站点](https://www.azul.com/downloads/azure-only/zulu/)下载 Azul Zulu Enterprise JDK Production Edition 进行本地开发。

### <a name="development-support"></a>开发支持

使用[符合条件的 Azure 支持计划](https://azure.microsoft.com/support/plans/)进行 Azure 或 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 方面的开发时，可以通过 Microsoft 获得对 [Azure 支持的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) 的产品支持。

### <a name="runtime-support"></a>运行时支持

如果开发人员有[符合条件的支持计划](https://azure.microsoft.com/support/plans/)，则可以通过 Azure 支持部门针对 Azul Zulu JDK [提出问题](/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>后续步骤

本主题提供了对 Windows 上的 Azure 应用服务的支持的 Java 运行时声明。

- 若要详细了解如何使用 Azure 应用服务托管 Web 应用程序，请参阅[应用服务概述](overview.md)。
- 有关 Java on Azure 开发的信息，请参阅 [Azure for Java 开发人员中心](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。

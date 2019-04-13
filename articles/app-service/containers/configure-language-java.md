---
title: 配置 Linux Java 应用程序-Azure 应用服务 |Microsoft Docs
description: 了解如何配置在 Linux 上的 Azure 应用服务中运行的 Java 应用。
keywords: azure 应用服务, web 应用, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551227"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>为 Azure 应用服务中配置 Linux Java 应用

Linux 上的 Azure 应用服务可让 Java 开发人员在完全托管的基于 Linux 的服务中快速生成、部署和缩放 Tomcat 或 Java Standard Edition (SE) 打包式 Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供关键概念和 Java 开发人员在应用服务中使用内置的 Linux 容器的说明。 如果你从未使用过 Azure 应用服务，请按照[Java 快速入门](quickstart-java.md)并[Java 与 PostgreSQL 教程](tutorial-java-enterprise-postgresql-app.md)第一个。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 有关详细信息，请参阅[Azure 应用服务诊断概述](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 控制台访问

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>流式传输诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

有关详细信息，请参阅[使用 Azure CLI 流式传输日志](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>应用日志记录

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](/azure/application-insights/app-insights-java-trace-logs)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。

## <a name="customization-and-tuning"></a>自定义和优化

适用于 Linux 的 azure 应用服务支持带框优化并通过 Azure 门户和 CLI 进行自定义。 查看非特定于 Java 的 web 应用配置的以下文章：

- [配置应用服务设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [设置自定义域](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [启用 SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [添加 CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要在 Tomcat 和 Java SE 环境中设置分配的内存或其他 JVM 运行时选项，请按如下所示将 JAVA_OPTS 设置为[应用程序设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)。 应用服务 Linux 在启动时，会将此设置作为环境变量传递给 Java 运行时。

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含其他设置的新应用设置，例如 `$JAVA_OPTS -Xms512m -Xmx1204m`。

若要通过 Azure 应用服务 Linux Maven 插件配置应用设置，请在 Azure 插件部分中添加设置/值标记。 下面的示例设置特定最小值和最大 Java 堆大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

在应用服务计划中运行包含一个部署槽位的单个应用程序的开发人员可以使用以下选项：

- B1 和 S1 实例：-Xms1024m -Xmx1024m
- B2 和 S2 实例：-Xms3072m -Xmx3072m
- B3 和 S3 实例：-Xms6144m -Xmx6144m


优化应用程序堆设置时，请查看应用服务计划详细信息，并考虑多个应用程序和部署槽位方面的需求，以得出最佳内存分配。

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

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含值 `$JAVA_OPTS -Dfile.encoding=UTF-8` 的新应用设置。

或者，可以使用应用服务 Maven 插件配置应用设置。 在插件配置中添加设置名称和值标记：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>安全应用程序

在适用于 Linux 应用服务中运行的 Java 应用程序实施与其他应用程序相同的一套[安全最佳做法](/azure/security/security-paas-applications-using-app-services)。 

### <a name="authenticate-users"></a>对用户进行身份验证

设置应用程序在 Azure 门户中使用的身份验证**身份验证和授权**选项。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。 有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以及其他标识提供者的相关文章。

如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)一文中的说明。

 Spring Boot 开发人员可以使用 [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) 通过熟悉的 Spring Security 注释和 API 来保护应用程序。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

遵照[绑定现有的自定义 SSL 证书](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)中的说明上传现有的 SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

## <a name="configure-apm-platforms"></a>配置 APM 的平台

本部分介绍如何连接 NewRelic 和 AppDynamics 应用程序性能监视 (APM) 平台与部署在 Linux 上的 Azure 应用服务上的 Java 应用程序。

[配置 New Relic](#configure-new-relic)
[配置 AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>配置 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 NewRelic 帐户
2. 从 NewRelic 下载 Java 代理，它将具有类似于 `newrelic-java-x.x.x.zip` 的文件名。
3. 复制你的许可证密钥，稍后需要使用它来配置代理。
4. [通过 ssh 登录到应用服务实例](app-service-linux-ssh-support.md)并创建一个新目录`/home/site/wwwroot/apm`。
5. 将解压缩的 NewRelic Java 代理文件上传到 `/home/site/wwwroot/apm` 下的一个目录中。 你的代理的文件应当位于 `/home/site/wwwroot/apm/newrelic` 中。
6. 修改位于 `/home/site/wwwroot/apm/newrelic/newrelic.yml` 处的 YAML 文件并将占位符许可证值替换为你自己的许可证密钥。
7. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你的应用使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果您使用的**WildFly**，请参阅 New Relic 文档[此处](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)有关安装 Java 代理和 JBoss 配置的指南。
    - 如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `javaagent` 选项追加到当前值的末尾。

### <a name="configure-appdynamics"></a>配置 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
1. 从 AppDynamics 网站下载 Java 代理，文件名将类似于 `AppServerAgent-x.x.x.xxxxx.zip`
1. [通过 ssh 登录到应用服务实例](app-service-linux-ssh-support.md)并创建一个新目录`/home/site/wwwroot/apm`。
1. 将 Java 代理文件上传到 `/home/site/wwwroot/apm` 下的一个目录中。 你的代理的文件应当位于 `/home/site/wwwroot/apm/appdynamics` 中。
1. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 的环境变量，其中，`<app-name>` 是你的应用服务名称。
    - 如果您使用的**WildFly**，请参阅 AppDynamics 文档[此处](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)有关安装 Java 代理和 JBoss 配置的指南。

## <a name="configure-tomcat"></a>配置 Tomcat

### <a name="connect-to-data-sources"></a>连接到数据源

>[!NOTE]
> 如果应用程序使用 Spring Framework 或 Spring Boot，你可以在 [应用程序的 properties 文件] 中将 Spring Data JPA 的数据库连接信息设置为环境变量。 然后在 Azure 门户或 CLI 中使用[应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)来为应用程序定义这些值。

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

或者在 Azure 门户中的“应用程序设置”边栏选项卡中设置环境变量。

接下来，确定数据源应当供一个应用程序使用，还是供在 Tomcat servlet 上运行的所有应用程序使用。

#### <a name="application-level-data-sources"></a>应用程序级别的数据源

1. 在项目的 `META-INF/` 目录中创建 `context.xml` 文件。 如果 `META-INF/` 目录不存在，则创建它。

2. 在 `context.xml` 中，添加一个 `Context` 元素以将数据源链接到 JNDI 地址。 将 `driverClassName` 占位符替换为上表中你的驱动程序的类名称。

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

3. 更新应用程序的 `web.xml`，以便在应用程序中使用该数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>服务器级别的共享的资源

1. 如果尚未进行相关的配置，请使用 SSH 将 `/usr/local/tomcat/conf` 的内容复制到应用服务 Linux 实例上的 `/home/tomcat/conf` 中。
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. 在 `server.xml` 中的 `<Server>` 元素内添加一个 Context 元素。

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

3. 更新应用程序的 `web.xml`，以便在应用程序中使用该数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成配置

最后，将驱动程序 jar 文件放在 Tomcat classpath 并重新启动你的应用服务。

1. 将 JDBC 驱动程序文件放入 `/home/tomcat/lib` 目录，确保它们可供 Tomcat 类加载器使用。 （如果此目录尚未存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：
    1. 在中[Cloud Shell](https://shell.azure.com)，安装 web 应用扩展：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 运行以下 CLI 命令来创建 SSH 隧道通过本地系统到应用服务：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用 SFTP 客户端连接到本地隧道端口，并将文件上传到 `/home/tomcat/lib` 文件夹。

    另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

2. 如果你创建了服务器级数据源，请重启应用服务 Linux 应用程序。 Tomcat 会将 `CATALINA_HOME` 重置为 `/home/tomcat/conf`，并使用更新后的配置。

## <a name="configure-wildfly-server"></a>配置 WildFly 服务器

[使用应用服务进行缩放](#scale-with-app-service)
[自定义应用程序服务器配置](#customize-application-server-configuration)
[模块和依赖项](#modules-and-dependencies)
[数据源](#data-sources)
[启用消息传递提供程序](#enable-messaging-providers)
[配置会话管理缓存](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>应用服务缩放

在 Linux 上的应用服务中运行的 WildFly 应用程序服务器以独立模式运行，而不是在域配置中运行。 横向扩展应用服务计划时，每个 WildFly 实例都被配置为独立的服务器。

 使用[缩放规则](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)和[增加实例数](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，纵向或横向缩放应用程序。

### <a name="customize-application-server-configuration"></a>自定义应用程序服务器配置

Web 应用实例是无状态的，因此必须在启动时配置启动的每个新实例，以支持应用程序所需的 Wildfly 配置。
你可以编写一个用于调用 WildFly CLI 的启动 Bash 脚本，以便执行以下操作：

- 设置数据源
- 配置消息提供程序
- 将其他模块和依赖项添加到 Wildfly 服务器配置中。

 脚本会在启动并运行 Wildfly 时（但需在应用程序启动前）运行。 该脚本应使用从 `/opt/jboss/wildfly/bin/jboss-cli.sh` 调用的 [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) 来配置应用程序服务器，其中包含服务器启动后所需的任何配置或更改。

请勿使用 CLI 的交互模式配置 Wildfly。 相反，可使用 `--file` 命令向 JBoss CLI 提供命令脚本，例如：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

将启动脚本上传到应用服务实例中的 `/home/site/deployments/tools`。 有关获取 FTP 凭据的说明，请参阅[本文档](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope)。

将 Azure 门户中的“启动脚本”字段设置为启动 shell 脚本的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。

提供[应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)中要将环境变量用于传递脚本中的应用程序配置。 应用程序设置将连接字符串和配置应用程序所需的其他机密置于版本控制之外。

### <a name="modules-and-dependencies"></a>模块和依赖项

要通过 JBoss CLI 将模块及其依赖项安装到 Wildfly 类路径中，需要在其自己的目录中创建以下文件。 某些模块和依赖项可能需要其他配置，例如 JNDI 命名或其他特定于 API 的配置，因此此列表是在大多数情况下配置依赖项所需的最小集。

- [XML 模块描述符](https://jboss-modules.github.io/jboss-modules/manual/#descriptors)。 此 XML 文件定义模块的名称、属性和依赖项。 此[示例 module.xml 文件](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)定义了 Postgres 模块、其 JAR 文件 JDBC 依赖项以及所需的其他模块依赖项。
- 模块的任何必要 JAR 文件依赖项。
- 具有 JBoss CLI 命令的脚本，用于配置新模块。 该文件将包含 JBoss CLI 要执行的命令，以配置服务器使用依赖项。 有关添加模块、数据源和消息提供程序的命令的文档，请参阅[本文档](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)。
- 用于调用 JBoss CLI 和执行上一步中脚本的 Bash 启动脚本。 重启应用服务实例或在横向扩展期间配置新实例时，将执行此文件。当 JBoss 命令传递给 JBoss CLI 时，可在此启动脚本中为应用程序执行任何其他配置。 此文件可以是将 JBoss CLI 命令脚本传递给 JBoss CLI 的最小单个命令：

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

获得模块的文件和内容后，请按照以下步骤将模块添加到 Wildfly 应用程序服务器。

1. 将文件 FTP 到应用服务实例中的 `/home/site/deployments/tools`。 有关获取 FTP 凭据的说明，请参阅本文档。
2. 在 Azure 门户的“应用程序设置”边栏选项卡中，将“启动脚本”字段设置为启动 shell 脚本的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。
3. 通过按下门户网站“概述”部分中的“重启”按钮或使用 Azure CLI 重启应用服务实例。

### <a name="data-sources"></a>数据源

要为数据源连接配置 Wildfly，请按照上面“安装模块和依赖项”部分中概述的相同过程进行操作。 可对任何 Azure 数据库服务执行相同的步骤。

1. 下载适用于数据库风格的 JDBC 驱动程序。 为方便起见，以下是 [Postgres](https://jdbc.postgresql.org/download.html) 和 [MySQL](https://dev.mysql.com/downloads/connector/j/) 的驱动程序。 解压缩下载项以获取 .jar 文件。
2. 按照“模块和依赖项”中概述的步骤创建和上传 XML 模块描述符、JBoss CLI 脚本、启动脚本和 JDBC .jar 依赖项。

有关使用 [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)、[MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) 和 [SQL 数据库](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)配置 Wildfly 的详细信息已可供参阅。 可使用这些自定义说明以及上述通用方法将数据源定义添加到服务器。

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

可将 Wildfly 配置为使用外部会话存储，例如 [Azure Redis 缓存](/azure/azure-cache-for-redis/)。 需要[禁用现有 ARR 实例相关性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)配置，才能关闭基于会话的 cookie 路由，以及允许配置的 Wildfly 会话存储运行不受干扰。

## <a name="docker-containers"></a>Docker 容器

若要在容器中使用 Azure 支持的 Zulu JDK，请确保拉取并使用[受支持的 Azul Zulu Enterprise for Azure 下载页面](https://www.azul.com/downloads/azure-only/zulu/)中提到的预构建的映像，或使用 [Microsoft Java GitHub 存储库](https://github.com/Microsoft/java/tree/master/docker)中的 `Dockerfile` 示例。

## <a name="statement-of-support"></a>支持声明

### <a name="runtime-availability"></a>运行时可用性

适用于 Linux 的应用服务支持对 Java Web 应用程序的托管主机使用两个运行时：

- [Tomcat servlet 容器](https://tomcat.apache.org/)：用于运行打包为 Web 存档 (WAR) 文件的应用程序。 支持的版本为 8.5 和 9.0。
- Java SE 运行时环境：用于运行打包为 Java 存档 (JAR) 文件的应用程序。 支持的版本为 Java 8 和 11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azure 支持的 Java 开发工具包 (JDK) 为提供 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主版本更新将通过适用于 Linux 的 Azure 应用服务中的新运行时选项提供。 客户可以通过配置应用服务部署来更新到这些较新的 Java 版本，他们需要负责测试和确保重大更新符合其需求。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

### <a name="local-development"></a>本地开发

开发人员可以从 [Azul 下载站点](https://www.azul.com/downloads/azure-only/zulu/)下载 Azul Zulu Enterprise JDK Production Edition 进行本地开发。

### <a name="development-support"></a>开发支持

使用[符合条件的 Azure 支持计划](https://azure.microsoft.com/support/plans/)进行 Azure 或 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 方面的开发时，可以获得对 [Azure 支持的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) 的产品支持。

### <a name="runtime-support"></a>运行时支持

如果开发人员有[符合条件的支持计划](https://azure.microsoft.com/support/plans/)，则可以通过 Azure 支持部门针对 Azul Zulu JDK [提出问题](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>后续步骤

请访问[面向 Java 开发人员的 Azure](/java/azure/) 中心查找 Azure 快速入门、教程和 Java 参考文档。

[应用服务 Linux 常见问题解答](app-service-linux-faq.md)中解答了并不特定于 Java 开发的、适用于 Linux 的应用服务的一般用法问题。
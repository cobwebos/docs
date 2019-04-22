---
title: Linux 上的应用服务的 Java 开发人员指南 - Azure | Microsoft Docs
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
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 5c9f70650f518c72a75d9a7826e7cbc30a95a00c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680870"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Linux 上的应用服务的 Java 开发人员指南

Linux 上的 Azure 应用服务可让 Java 开发人员在完全托管的基于 Linux 的服务中快速生成、部署和缩放 Tomcat 或 Java Standard Edition (SE) 打包式 Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供 Java 开发人员可在适用于 Linux 的应用服务中使用的重要概念和说明。 如果你从未用过适用于 Linux 的 Azure 应用服务，首先应该通读 [Java 快速入门](quickstart-java.md)。 [应用服务 Linux 常见问题解答](app-service-linux-faq.md)中解答了并不特定于 Java 开发的、适用于 Linux 的应用服务的一般用法问题。

## <a name="deploying-your-app"></a>部署应用

可以使用[适用于 Azure 应用服务的 Maven 插件](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)部署.jar 和.war 文件。 使用流行的 Ide 部署也支持使用[用于 IntelliJ 的 Azure 工具包](/java/azure/intellij/azure-toolkit-for-intellij)或[用于 Eclipse 的 Azure 工具包](/java/azure/eclipse/azure-toolkit-for-eclipse)。

否则，你的部署方法将取决于你的存档类型：

- 若要将 .war 文件部署到 Tomcat，请使用 `/api/wardeploy/` 终结点对存档文件执行 POST 操作。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像中的 .jar 文件，请使用 Kudu 站点的 `/api/zipdeploy/` 终结点。 有关此 API 的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

不要使用 FTP 来部署 .war 或 .jar。 FTP 工具设计用来上传启动脚本、依赖项或其他运行时文件。 它不是用于部署 Web 应用的最佳选项。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 请参阅 [Azure 应用服务诊断概述](/azure/app-service/overview-diagnostics)来详细了解如何访问和使用这些诊断工具。

## <a name="application-performance-monitoring"></a>应用程序性能监视

有关使用 Linux 版应用服务上运行的 Java 应用配置 New Relic 和 AppDynamics 的操作说明，请参阅[在 Linux 版 Azure 应用服务上使用 Java 应用的应用程序性能监视工具](how-to-java-apm-monitoring.md)。

### <a name="ssh-console-access"></a>SSH 控制台访问

提供 SSH 连接到运行您的应用程序的 Linux 环境。 请参阅 [Linux 上的 Azure 应用服务的 SSH 支持](/azure/app-service/containers/app-service-linux-ssh-support)，获取有关通过 Web 浏览器或本地终端连接到 Linux 系统的完整说明。

### <a name="streaming-logs"></a>流式处理日志

若要快速进行调试和故障排除，可以使用 Azure CLI 将日志流式传输到控制台。 使用 `az webapp log config` 配置 CLI 以启用日志记录：

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

然后，使用 `az webapp log tail` 将日志流式传输到控制台：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

有关详细信息，请参阅[使用 Azure CLI 流式处理日志](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>应用日志记录

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](/azure/app-service/troubleshoot-diagnostic-logs#enablediag)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。 Java 和 Tomcat 应用程序日志可在`/home/LogFiles/Application/`目录。

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](/azure/application-insights/app-insights-java-trace-logs)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。

### <a name="troubleshooting-tools"></a>故障排除工具

基于内置的 Java 映像[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)操作系统。 使用`apk`包管理器安装任何故障排除工具或命令。

## <a name="customization-and-tuning"></a>自定义和优化

适用于 Linux 的 azure 应用服务支持带框优化并通过 Azure 门户和 CLI 进行自定义。 请查看以下文章了解非特定于 Java 的 Web 应用配置：

- [配置应用服务设置](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [设置自定义域](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [启用 SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [添加 CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [配置 Kudu 站点](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要设置已分配的内存或其他 JVM 运行时选项在 Tomcat 和 Java SE 环境中，创建[应用程序设置](/azure/app-service/web-sites-configure#app-settings)名为`JAVA_OPTS`的选项。 应用服务 Linux 在启动时，会将此设置作为环境变量传递给 Java 运行时。

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

如果要部署的 JAR 应用程序，它应被命名为`app.jar`以便内置映像可以正确地标识您的应用程序。 （的 Maven 插件情况执行此重命名自动）。如果不希望重命名为 JAR `app.jar`，可以上传包含要运行 JAR 的命令的 shell 脚本。 然后粘贴到此脚本中的完整路径[启动文件](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file)在门户的配置部分中的文本框。

### <a name="turn-on-web-sockets"></a>启用 Web 套接字

在 Azure 门户中应用程序的“应用程序设置”中启用 Web 套接字支持。 需要重启应用程序才能使设置生效。

在 Azure CLI 中使用以下命令启用 Web 套接字支持：

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

然后重启应用程序：

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

## <a name="secure-applications"></a>安全应用程序

在适用于 Linux 应用服务中运行的 Java 应用程序实施与其他应用程序相同的一套[安全最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users"></a>对用户进行身份验证

设置应用程序在 Azure 门户中使用的身份验证**身份验证和授权**选项。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。  有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](/azure/app-service/configure-authentication-provider-aad)，以及其他标识提供者的相关文章。

如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)一文中的说明。

Spring Boot 开发人员可以使用 [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) 通过熟悉的 Spring Security 注释和 API 来保护应用程序。 请务必增加 `application.properties` 文件中的最大标头大小。 我们建议值为 `16384`。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

遵照[绑定现有的自定义 SSL 证书](/azure/app-service/app-service-web-tutorial-custom-ssl)中的说明上传现有的 SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用密钥保管库的引用

[Azure 密钥保管库](../../key-vault/key-vault-overview.md)提供与访问策略和审核历史记录的集中式密钥管理。 可以在密钥保管库中存储机密 （如密码或连接字符串），并通过环境变量在应用程序中访问这些机密。

首先，按照说明[授予应用访问 Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)并[在应用程序设置中进行对你的密码的密钥保管库引用](../app-service-key-vault-references.md#reference-syntax)。 您可以验证解析为机密的引用的远程访问应用服务终端时打印环境变量。

若要将注入 Spring 或 Tomcat 配置文件中的这些机密，使用环境变量注入语法 (`${MY_ENV_VAR}`)。 对于 Spring 配置文件，请参阅本文档中有关[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)。

## <a name="data-sources"></a>数据源

### <a name="tomcat"></a>Tomcat

这些说明适用于所有数据库连接。 你需要使用你选择的数据库的驱动程序类名称和 JAR 文件来填充占位符。 下面提供了一个表，其中包含了常见数据库的类名称和驱动程序下载。

| 数据库   | 驱动程序类名称                             | JDBC 驱动程序                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下载](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下载](https://dev.mysql.com/downloads/connector/j/)（选择“独立于平台”） |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下载](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

若要将 Tomcat 配置为使用 Java 数据库连接 (JDBC) 或 Java 持久性 API (JPA)，请先自定义在启动时由 Tomcat 读取的 `CATALINA_OPTS` 环境变量。 在[应用服务 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中通过某个应用设置来设置这些值：

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

#### <a name="shared-server-level-resources"></a>共享的服务器级别的资源

1. 如果尚未进行相关的配置，请使用 SSH 将 `/usr/local/tomcat/conf` 的内容复制到应用服务 Linux 实例上的 `/home/tomcat/conf` 中。

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>最后，将驱动程序 jar 文件放在 Tomcat classpath，然后重新启动你的应用服务

1. 将 JDBC 驱动程序文件放入 `/home/tomcat/lib` 目录，确保它们可供 Tomcat 类加载器使用。 （如果此目录尚未存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：  
   1. 安装 Azure 应用服务 webpp 扩展：

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. 运行以下 CLI 命令，创建从本地系统到应用服务的 SSH 隧道：

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. 使用 SFTP 客户端连接到本地隧道端口，并将文件上传到 `/home/tomcat/lib` 文件夹。

      另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。

2. 如果你创建了服务器级数据源，请重启应用服务 Linux 应用程序。 Tomcat 会将 `CATALINA_HOME` 重置为 `/home/tomcat/conf`，并使用更新后的配置。

### <a name="spring-boot"></a>Spring Boot

若要连接到 Spring Boot 应用程序中的数据源，我们建议创建的连接字符串并将其注入您`application.properties`文件。

1. 在应用服务边栏选项卡的"应用程序设置"部分中，设置字符串的名称，将 JDBC 连接字符串粘贴到值字段中，并设置为"Custom"的类型。 槽设置为，可以选择性地设置此连接字符串。

    ![在门户中创建的连接字符串。][1]

    此连接字符串都可以访问我们的应用程序为环境变量名为`CUSTOMCONNSTR_<your-string-name>`。 例如，我们在上面创建的连接字符串将被命名为`CUSTOMCONNSTR_exampledb`。

2. 在你`application.properties`文件中，引用环境变量名称与此连接字符串。 对于我们的示例，我们将使用以下命令。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

请参阅[Spring Boot 文档数据的访问权限](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
)并[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)有关本主题的详细信息。

## <a name="docker-containers"></a>Docker 容器

若要在容器中使用 Azure 支持的 Zulu JDK，请确保拉取并使用[受支持的 Azul Zulu Enterprise for Azure 下载页面](https://www.azul.com/downloads/azure-only/zulu/)中提到的预构建的映像，或使用 [Microsoft Java GitHub 存储库](https://github.com/Microsoft/java/tree/master/docker)中的 `Dockerfile` 示例。

## <a name="runtime-availability-and-statement-of-support"></a>运行时可用性和支持声明

适用于 Linux 的应用服务支持对 Java Web 应用程序的托管主机使用两个运行时：

- [Tomcat servlet 容器](https://tomcat.apache.org/)：用于运行打包为 Web 存档 (WAR) 文件的应用程序。 支持的版本为 8.5 和 9.0。
- Java SE 运行时环境：用于运行打包为 Java 存档 (JAR) 文件的应用程序。 唯一支持的主版本为 Java 8。

## <a name="java-runtime-statement-of-support"></a>Java 运行时支持声明

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

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png

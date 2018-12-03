---
title: Linux 上的 Azure 应用服务的 Java 语言支持 | Microsoft Docs
description: 有关如何使用 Linux 上的 Azure 应用服务部署 Java 应用的开发人员指南。
keywords: azure 应用服务, web 应用, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 8d15aeb92911a26a9a42a0449a24e8c0fee4467b
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497338"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Linux 上的应用服务的 Java 开发人员指南

Linux 上的 Azure 应用服务可让 Java 开发人员在完全托管的基于 Linux 的服务中快速生成、部署和缩放 Tomcat 或 Java Standard Edition (SE) 打包式 Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供 Java 开发人员可在适用于 Linux 的应用服务中使用的重要概念和说明。 如果你从未用过适用于 Linux 的 Azure 应用服务，首先应该通读 [Java 快速入门](quickstart-java.md)。 [应用服务 Linux 常见问题解答](app-service-linux-faq.md)中解答了并不特定于 Java 开发的、适用于 Linux 的应用服务的一般用法问题。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 请参阅 [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)来详细了解如何访问和使用这些诊断工具。

## <a name="application-performance-monitoring"></a>应用程序性能监视

有关使用 Linux 版应用服务上运行的 Java 应用配置 New Relic 和 AppDynamics 的操作说明，请参阅[在 Linux 版 Azure 应用服务上使用 Java 应用的应用程序性能监视工具](how-to-java-apm-monitoring.md)。

### <a name="ssh-console-access"></a>SSH 控制台访问 

可与运行应用的 Linux 环境建立 SSH 连接。 请参阅 [Linux 上的 Azure 应用服务的 SSH 支持](/azure/app-service/containers/app-service-linux-ssh-support)，获取有关通过 Web 浏览器或本地终端连接到 Linux 系统的完整说明。

### <a name="streaming-logs"></a>流式传输日志

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

有关详细信息，请参阅[使用 Azure CLI 流式传输日志](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface)。

### <a name="app-logging"></a>应用日志记录

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](/azure/app-service/web-sites-enable-diagnostic-log#enablediag)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](/azure/application-insights/app-insights-java-trace-logs)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。 

## <a name="customization-and-tuning"></a>自定义和优化

适用于 Linux 的 Azure 应用服务原生支持通过 Azure 门户和 CLI 进行优化和自定义。 请查看以下文章了解非特定于 Java 的 Web 应用配置：

- [配置应用服务设置](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [设置自定义域](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [启用 SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [添加 CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要在 Tomcat 和 Java SE 环境中设置分配的内存或其他 JVM 运行时选项，请按如下所示将 JAVA_OPTS 设置为[应用程序设置](/azure/app-service/web-sites-configure#app-settings)。 应用服务 Linux 在启动时，会将此设置作为环境变量传递给 Java 运行时。

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含其他设置的新应用设置，例如 `$JAVA_OPTS -Xms512m -Xmx1204m`。

若要通过 Azure 应用服务 Linux Maven 插件配置应用设置，请在 Azure 插件部分中添加设置/值标记。 以下示例设置特定的最小和最大 Java 堆大小：

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
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

然后重启应用程序：

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

在 Azure 门户中使用“身份验证和授权”选项设置应用身份验证。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。  有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)，以及其他标识提供者的相关文章。

如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)一文中的说明。

 Spring Boot 开发人员可以使用 [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) 通过熟悉的 Spring Security 注释和 API 来保护应用程序。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

遵照[绑定现有的自定义 SSL 证书](/azure/app-service/app-service-web-tutorial-custom-ssl)中的说明上传现有的 SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>连接到数据源

>[!NOTE]
> 如果应用程序使用 Spring Framework 或 Spring Boot，你可以在 [应用程序的 properties 文件] 中将 Spring Data JPA 的数据库连接信息设置为环境变量。 然后在 Azure 门户或 CLI 中使用[应用设置](/azure/app-service/web-sites-configure#app-settings)来为应用程序定义这些值。

本节中的示例配置片段使用 MySQL 数据库。 有关其他信息，请参阅 [MySQL](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-usagenotes-tomcat.html)、[SQL Server JDBC](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?view=sql-server-2017) 和 [PostgreSQL](https://jdbc.postgresql.org/documentation/head/index.html) 的配置文档。

若要使用 Java 数据库连接 (JDBC) 或 Java 持久性 API (JPA) 将 Tomcat 配置为使用数据库的托管连接，请先自定义 Tomcat 在启动时要读取的 CATALINA_OPTS 环境变量。 在应用服务 Maven 插件中通过某个应用设置来设置这些值：

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

或者使用 Azure 门户中类似的应用服务设置。

接下来，确定是要将数据源提供给一个应用程序，还是提供给应用服务计划中运行的所有应用程序。

对于应用程序级的数据源： 

1. 将 `context.xml` 文件添加到 Web 应用程序（如果该文件不存在），并在生成项目时将该文件添加到 WAR 文件所在的 `META-INF` 目录。

2. 在此文件中添加 `Context` 路径项，以将数据源链接到 JNDI 地址。

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. 更新应用程序的 `web.xml`，以便在应用程序中使用该数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

对于共享服务器级的资源：

1. 如果尚未进行相关的配置，请使用 SSH 将 `/usr/local/tomcat/conf` 的内容复制到应用服务 Linux 实例上的 `/home/tomcat/conf` 中。

2. 将上下文添加到 `server.xml`

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. 更新应用程序的 `web.xml`，以便在应用程序中使用该数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. 将 JDBC 驱动程序文件放入 `/home/tomcat/lib` 目录，确保它们可供 Tomcat 类加载器使用。 若要将这些文件上传到应用服务实例，请执行以下步骤：  
    1. 安装 Azure 应用服务 webpp 扩展：

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. 运行以下 CLI 命令，创建从本地系统到应用服务的 SSH 隧道：

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. 使用 SFTP 客户端连接到本地隧道端口，并将文件上传到 `/home/tomcat/lib` 文件夹。

5. 重启应用服务 Linux 应用程序。 Tomcat 会将 `CATALINA_HOME` 重置为 `/home/tomcat/conf`，并使用更新的配置和类。

## <a name="docker-containers"></a>Docker 容器

若要在容器中使用 Azure 支持的 Zulu JDK，请确保拉取并使用[受支持的 Azul Zulu Enterprise for Azure 下载页面](https://www.azul.com/downloads/azure-only/zulu/)中提到的预构建的映像，或使用 [Microsoft Java GitHub 存储库](https://github.com/Microsoft/java/tree/master/docker)中的 `Dockerfile` 示例。

## <a name="runtime-availability-and-statement-of-support"></a>运行时可用性和支持声明

适用于 Linux 的应用服务支持对 Java Web 应用程序的托管主机使用两个运行时：

- [Tomcat servlet 容器](http://tomcat.apache.org/)：用于运行打包为 Web 存档 (WAR) 文件的应用程序。 支持的版本为 8.5 和 9.0。
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

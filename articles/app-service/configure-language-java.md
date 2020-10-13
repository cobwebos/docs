---
title: 配置 Java 应用
description: 了解如何将 Java 应用配置为在 Azure App Service 上运行。 本文介绍最常见的配置任务。
keywords: azure 应用服务，web 应用，windows，oss，java，tomcat，jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 736d80c4bcfe31a499b84bb24c1c377e69e84218
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976005"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>为 Azure 应用服务配置 Java 应用

Azure App Service 允许 Java 开发人员在完全托管的服务上快速生成、部署和缩放其 Java SE、Tomcat 和 JBoss EAP web 应用程序。 通过 Maven 插件、命令行或编辑器（如 IntelliJ、Eclipse 或 Visual Studio Code）部署应用程序。

本指南为使用应用服务的 Java 开发人员提供重要的概念和说明。 如果你从未用过 Azure 应用服务，首先应该通读 [Java 快速入门](quickstart-java.md)。 有关使用不特定于 Java 开发的应用服务的一般问题，请在 [应用服务常见问题解答](faq-configuration-and-management.md)中进行回答。

## <a name="deploying-your-app"></a>部署应用

可以使用用于 [Maven 的 Azure Web 应用插件](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 来部署 war 或 .jar 文件。 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/)或[Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)还支持通过常用 ide 进行部署。

否则，则部署方法将取决于存档类型：

### <a name="java-se"></a>Java SE

若要将 .jar 文件部署到 Java SE，请使用 Kudu 站点的 `/api/zipdeploy/` 终结点。 有关此 API 的详细信息，请参阅[此文档](./deploy-zip.md#rest)。

### <a name="tomcat"></a>Tomcat

若要将 .war 文件部署到 Tomcat，请使用 `/api/wardeploy/` 终结点对存档文件执行 POST 操作。 有关此 API 的详细信息，请参阅[此文档](./deploy-zip.md#deploy-war-file)。

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

若要将 war 文件部署到 JBoss，请使用 `/api/wardeploy/` 终结点发布存档文件。 有关此 API 的详细信息，请参阅[此文档](./deploy-zip.md#deploy-war-file)。

若要部署 ear 文件，请 [使用 FTP](deploy-ftp.md)。

::: zone-end

不要使用 FTP 来部署 .war 或 .jar。 FTP 工具设计用来上传启动脚本、依赖项或其他运行时文件。 它不是用于部署 Web 应用的最佳选项。

## <a name="logging-and-debugging-apps"></a>日志记录和调试应用

可以通过 Azure 门户对每个应用使用性能报告、流量可视化和运行状况检查。 有关详细信息，请参阅 [Azure 应用服务诊断概述](overview-diagnostics.md)。

### <a name="stream-diagnostic-logs"></a>流式传输诊断日志

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

有关详细信息，请参阅[在 Cloud Shell 中流式传输日志](troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="ssh-console-access"></a>SSH 控制台访问

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

::: zone pivot="platform-linux"

### <a name="troubleshooting-tools"></a>故障排除工具

内置的 Java 映像建立在 [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 操作系统上。 使用 `apk` 包管理器安装任何故障排除工具或命令。

::: zone-end

### <a name="flight-recorder"></a>网络流量记录器

应用服务上使用 Azul JVM 的所有 Java 运行时均附带 Zulu 网络流量记录器。 你可以使用它来记录 JVM、系统和应用程序事件，并在 Java 应用程序中解决问题。

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>计时记录

若要进行定时记录，需要 Java 应用程序的 PID（进程 ID）。 若要查找 PID，请在 https://中打开 web 应用的 SCM 站点的浏览器 <> 你的 scm.azurewebsites.net/ProcessExplorer/。 此页面显示 Web 应用中正在运行的进程。 在表中找到名为“java”的进程，并复制相应的 PID（进程 ID）。

接下来，打开 SCM 站点顶部工具栏中的“调试控制台”，运行以下命令。 将 `<pid>` 替换为此前复制的进程 ID。 此命令会启动对 Java 应用程序的 30 秒探查器记录，并在 `D:\home` 目录中生成名为 `timed_recording_example.jfr` 的文件。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

通过 SSH 连接到应用服务并运行 `jcmd` 命令，以查看所有正在运行的 Java 进程的列表。 除了 jcmd 本身，你还会看到正在运行的 Java 应用程序以及进程 ID 号 (PID)。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

执行以下命令以启动对 JVM 的 30 秒记录。 此操作将分析 JVM 并创建一个名为“jfr_example.jfr”的 JFR 文件。 （将 116 替换为 Java 应用的 PID。）

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在 30 秒的间隔内，你可以通过运行 `jcmd 116 JFR.check` 来验证记录是否正在进行。 这会显示给定 Java 进程的所有记录。

#### <a name="continuous-recording"></a>连续记录

你可以使用 Zulu 网络流量记录器在对运行时的性能影响最小的情况下连续分析 Java 应用程序（[源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)）。 为此，请运行以下 Azure CLI 命令，通过必要的配置创建名为 JAVA_OPTS 的应用设置。 应用启动后，JAVA_OPTS 应用设置的内容会被传递到 `java` 命令。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

开始记录后，你可以使用 `JFR.dump` 命令随时转储当前的记录数据。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>分析 `.jfr` 文件

使用 [FTPS](deploy-ftp.md) 将 JFR 文件下载到本地计算机。 如果要分析 JFR 文件，请下载并安装 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)。 有关 Zulu Mission Control 的说明，请参阅 [Azul 文档](https://docs.azul.com/zmc/)和[安装说明](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

### <a name="app-logging"></a>应用日志记录

::: zone pivot="platform-windows"

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 在完成配置并经过 12 个小时后，将禁用记录到应用服务本地文件系统实例。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。 Java 和 Tomcat 应用日志位于 /home/LogFiles/Application/ 目录中。

::: zone-end
::: zone pivot="platform-linux"

通过 Azure 门户或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 启用[应用程序日志记录](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer)，以将应用服务配置为向本地文件系统或 Azure Blob 存储写入应用程序的标准控制台输出和标准控制台错误流。 如果需要保留日志更长时间，请将应用程序配置为向 Blob 存储容器写入输出。 Java 和 Tomcat 应用日志位于 /home/LogFiles/Application/ 目录中。

只能使用 [Azure Monitor（预览版）](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)配置适用于基于 Linux 的应用服务的 Azure Blob 存储日志记录功能 

::: zone-end

如果应用程序使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 进行跟踪，则你可以遵照[在 Application Insights 中浏览 Java 跟踪日志](../azure-monitor/app/java-trace-logs.md)中的日志记录框架配置说明，将这些用于审查的跟踪写入到 Azure Application Insights。

## <a name="customization-and-tuning"></a>自定义和优化

适用于 Linux 的 Azure 应用服务原生支持通过 Azure 门户和 CLI 进行优化和自定义。 请查看以下文章，了解非特定于 Java 的 Web 应用配置：

- [配置应用设置](configure-common.md#configure-app-settings)
- [设置自定义域](app-service-web-tutorial-custom-domain.md)
- [配置 SSL 绑定](configure-ssl-bindings.md)
- [添加 CDN](../cdn/cdn-add-to-web-app.md)
- [配置 Kudu 站点](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>设置 Java 运行时选项

若要设置分配的内存或其他 JVM 运行时选项，请使用这些选项创建名为 `JAVA_OPTS` 的[应用设置](configure-common.md#configure-app-settings)。 应用服务在启动时，会将此设置作为环境变量传递给 Java 运行时。

在 Azure 门户中 Web 应用的“应用程序设置”下，创建名为 `JAVA_OPTS` 且包含其他设置的新应用设置，例如 `-Xms512m -Xmx1204m`。

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

### <a name="pre-compile-jsp-files"></a>预编译 JSP 文件

要提高 Tomcat 应用程序的性能，可以先编译 JSP 文件，再部署到应用服务。 你可以使用由 Apache Sling 提供的 [Maven 插件](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，也可以使用此 [Ant 生成文件](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>安全应用程序

在应用服务中运行的 Java 应用程序与其他应用程序具有相同的一组 [安全最佳做法](../security/fundamentals/paas-applications-using-app-services.md) 。

### <a name="authenticate-users-easy-auth"></a>对用户进行身份验证（简易身份验证）

在 Azure 门户中使用“身份验证和授权”选项设置应用身份验证。 在此处，可以使用 Azure Active Directory 或社交登录名（例如 Facebook、Google、或 GitHub）启用身份验证。 仅当配置单个身份验证提供程序时，Azure 门户配置才起作用。 有关详细信息，请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](configure-authentication-provider-aad.md)，以及其他标识提供者的相关文章。 如果需要启用多个登录提供程序，请遵照[自定义应用服务身份验证](app-service-authentication-how-to.md)一文中的说明。

#### <a name="java-se"></a>Java SE

Spring Boot 开发人员可以使用 [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) 通过熟悉的 Spring Security 注释和 API 来保护应用程序。 务必增加 application.properties 文件中的最大标头大小。 我们建议值为 `16384`。

#### <a name="tomcat"></a>Tomcat

Tomcat 应用程序可通过将 Principal 对象强制转换为 Map 对象这种方式，直接从 Servlet 访问用户的声明。 Map 对象会将每个声明类型映射到该类型的声明集合。 在下面的代码中，`request` 是 `HttpServletRequest` 的实例。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

现在，你可以检查 `Map` 对象是否有任何特定的声明。 例如，以下代码片段循环访问所有声明类型，并输出各集合的内容。

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

若要注销用户，请使用 `/.auth/ext/logout` 路径。 若要执行其他操作，请参阅介绍[应用服务身份验证和授权的用法](./app-service-authentication-how-to.md)的文档。 还可以阅读关于 Tomcat [HttpServletRequest 接口](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法的官方文档。 以下 Servlet 方法是基于你的应用服务配置解除冻结的：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

要禁用此功能，请创建一个名为 `WEBSITE_AUTH_SKIP_PRINCIPAL` 的应用程序设置，其值为 `1`。 若要禁用应用服务添加的所有 servlet 筛选器，请创建名为 `WEBSITE_SKIP_FILTERS`、值为 `1` 的设置。

### <a name="configure-tlsssl"></a>配置 TLS/SSL

按照[在 Azure 应用服务中使用 SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)中的说明上传现有的 SSL 证书，并将其绑定到应用程序的域名。 默认情况下，应用程序仍允许 HTTP 连接 - 请遵循教程中的具体步骤来强制实施 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 Key Vault 引用

[Azure Key Vault](../key-vault/general/overview.md) 通过访问策略和审核历史记录提供集中式机密管理。 你可以在 Key Vault 中存储机密（例如密码或连接字符串），并通过环境变量在应用程序中访问这些机密。

首先，请按照说明执行以下操作：[授予应用对 Key Vault 的访问权限](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)以及[在应用程序设置中创建对机密的 Key Vault 引用](app-service-key-vault-references.md#reference-syntax)。 你可以验证在远程访问应用服务终端时，该引用是否通过输出环境变量解析为机密。

若要在 Spring 或 Tomcat 配置文件中注入这些机密，请使用环境变量注入语法（即 `${MY_ENV_VAR}`）。 请参阅有关[外化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的文档，了解 Spring 配置文件。

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>使用 Java 密钥存储

在容器启动时，系统会默认将[上传到应用服务 Linux](configure-ssl-certificate.md) 的公用或专用证书都加载到各自的 Java 密钥存储中。 上传证书后，你需要重启应用服务，以便将它加载到 Java 密钥存储中。 系统会将公用证书加载到位于 `$JAVA_HOME/jre/lib/security/cacerts` 的密钥存储中，将专用证书存储到 `$JAVA_HOME/lib/security/client.jks` 中。

如果要在 Java 密钥存储中使用证书加密 JDBC 连接，可能需要其他配置。 请参阅适用于你所选的 JDBC 驱动程序的文档。

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>初始化 Java 密钥存储

要初始化 `import java.security.KeyStore` 对象，请使用密码加载密钥存储文件。 两个密钥存储的默认密码均为“changeit”。

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>手动加载密钥存储

可以手动向密钥存储加载证书。 创建应用设置 `SKIP_JAVA_KEYSTORE_LOAD`，将其值设置为 `1`，以禁止应用服务自动将证书加载到密钥存储。 所有通过 Azure 门户上传到应用服务的公用证书都存储在 `/var/ssl/certs/` 下。 专用证书则存储在 `/var/ssl/private/` 下。

[打开与应用服务的 SSH 连接](configure-linux-open-ssh-session.md)并运行命令 `keytool`，即可与 Java 密钥工具进行交互或对它进行调试。 请参阅[密钥工具文档](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)，获取命令列表。 有关密钥存储 API 的详细信息，请参阅[官方文档](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)。

::: zone-end

## <a name="configure-apm-platforms"></a>配置 APM 平台

本部分介绍如何使用 NewRelic 和 AppDynamics 应用程序性能监视 (APM) 平台连接在 Linux 上的 Azure 应用服务上部署的 Java 应用程序。

### <a name="configure-new-relic"></a>配置 NewRelic

::: zone pivot="platform-windows"

1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 NewRelic 帐户
2. 从 NewRelic 下载 Java 代理，该代理的文件名类似于 newrelic-java-x.x.x.zip。
3. 复制你的许可证密钥，稍后需要使用它来配置代理。
4. [通过 SSH 登录到应用服务实例](configure-linux-open-ssh-session.md)，并创建新目录 /home/site/wwwroot/apm。
5. 将解压缩的 NewRelic Java 代理文件上传到 /home/site/wwwroot/apm 下的一个目录中。 代理的文件应位于 /home/site/wwwroot/apm/newrelic 中。
6. 修改位于 /home/site/wwwroot/apm/newrelic/newrelic.yml 处的 YAML 文件，并将占位符许可证值替换为你自己的许可证密钥。
7. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。

    - 对于 **JAVA SE** 应用，请创建一个名为、值为的环境变量 `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 。
    - 对于 **Tomcat**，请创建一个名为、值为的环境变量 `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 。

::: zone-end
::: zone pivot="platform-linux"

1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 NewRelic 帐户
2. 从 NewRelic 下载 Java 代理，该代理的文件名类似于 newrelic-java-x.x.x.zip。
3. 复制你的许可证密钥，稍后需要使用它来配置代理。
4. [通过 SSH 登录到应用服务实例](configure-linux-open-ssh-session.md)，并创建新目录 /home/site/wwwroot/apm。
5. 将解压缩的 NewRelic Java 代理文件上传到 /home/site/wwwroot/apm 下的一个目录中。 代理的文件应位于 /home/site/wwwroot/apm/newrelic 中。
6. 修改位于 /home/site/wwwroot/apm/newrelic/newrelic.yml 处的 YAML 文件，并将占位符许可证值替换为你自己的许可证密钥。
7. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
   
    - 对于 **JAVA SE** 应用，请创建一个名为、值为的环境变量 `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 。
    - 对于 **Tomcat**，请创建一个名为、值为的环境变量 `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 。

::: zone-end

>  如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `-javaagent:/...` 选项追加到当前值的末尾。

### <a name="configure-appdynamics"></a>配置 AppDynamics

::: zone pivot="platform-windows"

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
2. 从 AppDynamics 网站下载 Java 代理，文件名将类似于 AppServerAgent-x.x.x.xxxxx.zip
3. 使用 [Kudu 控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)创建新目录 */home/site/wwwroot/apm*。
4. 将 Java 代理文件上传到 /home/site/wwwroot/apm 下的一个目录中。 代理的文件应位于 /home/site/wwwroot/apm/appdynamics 中。
5. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。

   - 对于 **JAVA SE** 应用，创建一个名为的环境变量，其 `JAVA_OPTS` 值 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 为，其中 `<app-name>` 是你的应用服务名称。
   - 对于 **Tomcat** 应用，请创建一个名为的环境变量，其 `CATALINA_OPTS` 值 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 为，其中 `<app-name>` 是你的应用服务名称。

::: zone-end
::: zone pivot="platform-linux"

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
2. 从 AppDynamics 网站下载 Java 代理，文件名将类似于 AppServerAgent-x.x.x.xxxxx.zip
3. [通过 SSH 登录到应用服务实例](configure-linux-open-ssh-session.md)，并创建新目录 /home/site/wwwroot/apm。
4. 将 Java 代理文件上传到 /home/site/wwwroot/apm 下的一个目录中。 代理的文件应位于 /home/site/wwwroot/apm/appdynamics 中。
5. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。

   - 对于 **JAVA SE** 应用，创建一个名为的环境变量，其 `JAVA_OPTS` 值 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 为，其中 `<app-name>` 是你的应用服务名称。
   - 对于 **Tomcat** 应用，请创建一个名为的环境变量，其 `CATALINA_OPTS` 值 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 为，其中 `<app-name>` 是你的应用服务名称。

::: zone-end

> [!NOTE]
>  如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `-javaagent:/...` 选项追加到当前值的末尾。

## <a name="configure-data-sources"></a>配置数据源

### <a name="java-se"></a>Java SE

若要连接 Spring Boot 中的数据源，我们建议创建连接字符串并将它们注入 application.properties 文件中。

1. 在应用服务页面的“配置”部分，为字符串设置一个名称，将 JDBC 连接字符串粘贴到值字段中，然后将类型设置为“自定义”。 你可以选择将此连接字符串设置为槽设置。

    此连接字符串可作为名为 `CUSTOMCONNSTR_<your-string-name>` 的环境变量供应用程序访问。 例如，我们在上面创建的连接字符串将被命名为 `CUSTOMCONNSTR_exampledb`。

2. 在 application.properties 文件中，通过环境变量名称引用此连接字符串。 在我们的示例中，我们将使用以下代码。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

请查看[有关数据访问的 Spring Boot 文档](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)和[外化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)，详细了解此主题。

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

这些说明适用于所有数据库连接。 你需要使用你选择的数据库的驱动程序类名称和 JAR 文件来填充占位符。 下面提供了一个表，其中包含了常见数据库的类名称和驱动程序下载。

| 数据库   | 驱动程序类名称                             | JDBC 驱动程序                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下载](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下载](https://dev.mysql.com/downloads/connector/j/)（选择“独立于平台”） |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下载](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

若要将 Tomcat 配置为使用 Java Database Connectivity (JDBC) 或 Java 持久性 API (JPA)，请先自定义在启动时由 Tomcat 读取的 `CATALINA_OPTS` 环境变量。 在[应用服务 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中通过某个应用设置来设置这些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或者在 Azure 门户的“配置” > “应用程序设置”页中设置环境变量 。

接下来，确定数据源应当供一个应用程序使用，还是供在 Tomcat servlet 上运行的所有应用程序使用。

#### <a name="application-level-data-sources"></a>应用程序级的数据源

1. 在项目的 META-INF/ 目录中创建一个 META-INF/ 文件 。 如果没有 META-INF/ 目录，则创建一个。

2. 在 context.xml 中，添加一个 `Context` 元素以将数据源链接到 JNDI 地址。 将 `driverClassName` 占位符替换为上表中你的驱动程序的类名称。

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

最后，将驱动程序 JAR 放在 Tomcat 类路径中并重启应用服务。 将 JDBC 驱动程序文件放入 */home/tomcat/lib* 目录，确保它们可供 Tomcat 类加载器使用。 （如果此目录尚未存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：

1. 在 [Cloud Shell](https://shell.azure.com) 中，安装 webapp 扩展：

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 运行以下 CLI 命令，创建从本地系统到应用服务的 SSH 隧道：

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. 使用 SFTP 客户端连接到本地隧道端口，并将文件上传到 /home/tomcat/lib 文件夹。

另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](deploy-configure-credentials.md)。

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

这些说明适用于所有数据库连接。 你需要使用你选择的数据库的驱动程序类名称和 JAR 文件来填充占位符。 下面提供了一个表，其中包含了常见数据库的类名称和驱动程序下载。

| 数据库   | 驱动程序类名称                             | JDBC 驱动程序                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下载](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下载](https://dev.mysql.com/downloads/connector/j/)（选择“独立于平台”） |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下载](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

若要将 Tomcat 配置为使用 Java Database Connectivity (JDBC) 或 Java 持久性 API (JPA)，请先自定义在启动时由 Tomcat 读取的 `CATALINA_OPTS` 环境变量。 在[应用服务 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中通过某个应用设置来设置这些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或者在 Azure 门户的“配置” > “应用程序设置”页中设置环境变量 。

接下来，确定数据源应当供一个应用程序使用，还是供在 Tomcat servlet 上运行的所有应用程序使用。

#### <a name="application-level-data-sources"></a>应用程序级的数据源

1. 在项目的 META-INF/ 目录中创建一个 META-INF/ 文件 。 如果没有 META-INF/ 目录，则创建一个。

2. 在 context.xml 中，添加一个 `Context` 元素以将数据源链接到 JNDI 地址。 将 `driverClassName` 占位符替换为上表中你的驱动程序的类名称。

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

3. 更新应用程序的 web.xml以便在应用程序中使用该数据源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共享的服务器级资源

添加共享的服务器级数据源需要编辑 Tomcat 的 server.xml。 首先，上传[启动脚本](faq-app-service-linux.md#built-in-images)，然后在“配置” > “启动命令”中设置该脚本的路径 。 你可以使用 [FTP](deploy-ftp.md) 上传启动脚本。

启动脚本将进行 [xsl 转换](https://www.w3schools.com/xml/xsl_intro.asp)，转换为 server.xml 文件，并将所得的 xml 文件输出到 `/usr/local/tomcat/conf/server.xml`。 启动脚本应通过 apk 安装 libxslt。 你的 xsl 文件和启动脚本均可通过 FTP 上传。 下面是一个示例启动脚本。

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

下面提供了一个示例 xsl 文件。 该文件向 Tomcat server.xml 添加了一个新的连接器节点。

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>完成配置

最后，将驱动程序 JAR 放置在 Tomcat 类路径中并重启你的应用服务。

1. 将 JDBC 驱动程序文件放入 /home/tomcat/lib 目录，确保它们可供 Tomcat 类加载器使用。 （如果此目录尚未存在，请创建它。）若要将这些文件上传到应用服务实例，请执行以下步骤：

    1. 在 [Cloud Shell](https://shell.azure.com) 中，安装 webapp 扩展：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 运行以下 CLI 命令，创建从本地系统到应用服务的 SSH 隧道：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用 SFTP 客户端连接到本地隧道端口，并将文件上传到 /home/tomcat/lib 文件夹。

    另外，也可以使用某个 FTP 客户端上传 JDBC 驱动程序。 请遵循这些[用于获取 FTP 凭据的说明](deploy-configure-credentials.md)。

2. 如果你创建了服务器级数据源，请重启应用服务 Linux 应用程序。 Tomcat 会将 `CATALINA_BASE` 重置为 `/home/tomcat`，并使用更新后的配置。

### <a name="jboss-eap"></a>JBoss EAP

[向 JBOSS EAP 注册数据源](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)时，有三个核心步骤：上传 jdbc 驱动程序、将 jdbc 驱动程序添加为模块、注册模块。 应用服务是一个无状态宿主服务，因此，在容器启动时，必须编写用于添加和注册数据源模块的配置命令并将其应用。

1. 获取数据库的 JDBC 驱动程序。 
2. 为 JDBC 驱动程序创建 XML 模块定义文件。 下面显示的示例是 PostgreSQL 的模块定义。

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. 将 JBoss CLI 命令放到名为的文件中 `jboss-cli-commands.cli` 。 JBoss 命令必须添加模块，并将其注册为数据源。 以下示例显示了用于 PostgreSQL 的 JBoss CLI 命令。

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. 创建 `startup_script.sh` 调用 JBOSS CLI 命令的启动脚本。 下面的示例演示如何调用 `jboss-cli-commands.cli` 。 稍后，将 configre 应用服务在容器启动时运行此脚本。 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. 使用所选的 FTP 客户端，将 JDBC 驱动程序、 `jboss-cli-commands.cli` 、 `startup_script.sh` 和模块定义上传到 `/site/deployments/tools/` 。
2. 将站点配置为在 `startup_script.sh` 容器启动时运行。 在 Azure 门户中，导航到 "**配置**" "  >  **常规设置**" "  >  **启动命令**"。 将 "启动" 命令字段设置为 `/home/site/deployments/tools/startup_script.sh` 。 单击“保存”以保存更改。

若要确认已将数据源添加到 JBoss 服务器，请通过 SSH 连接到 webapp 并运行 `$JBOSS_HOME/bin/jboss-cli.sh --connect` 。 连接到 JBoss 后，请运行 `/subsystem=datasources:read-resource` 以打印数据源的列表。

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>选择 Java 运行时版本

应用服务允许用户选择 JVM 的主要版本，如 Java 8 或 Java 11，以及次要版本，如 1.8.0 _232 或11.0.5。 你还可以选择在新的次要版本可用时自动更新次要版本。 在大多数情况下，生产站点应使用固定的次要 JVM 版本。 这会阻止在次要版本自动更新期间发生 unnanticipated 中断。

如果你选择固定次版本，你将需要定期更新站点上的 JVM 次版本。 若要确保应用程序在较新的次要版本上运行，请创建一个过渡槽并递增暂存站点上的次版本。 确认应用程序在新的次版本上正确运行后，可以交换过渡和生产槽。

## <a name="java-runtime-statement-of-support"></a>Java 运行时支持声明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azure 支持的 Java 开发工具包 (JDK) 为提供 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。 Azul Zulu Enterprise 内部版 OpenJDK 是适用于 Azure 和 Azure Stack 的 OpenJDK 的免费、多平台、生产就绪型发行版，由 Microsoft 及 Azul Systems 提供支持。 这些版本包含构建和运行 Java SE 应用程序所需的所有组件。 你可以通过 [Java JDK 安装](https://aka.ms/azure-jdks)安装 JDK。

主要版本更新将通过 Azure App Service 中的新运行时选项提供。 客户可以通过配置应用服务部署来更新到这些较新的 Java 版本，他们需要负责测试和确保重大更新符合其需求。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。 有关 Azure 上的 Java 的详细信息，请参阅[此支持文档](/azure/developer/java/fundamentals/java-jdk-long-term-support)。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/vuln-metrics/cvss) 提供的基本评分 9.0 或以上来定义的。

Tomcat 8.0 [已经在 2018 年 9 月 30 日生命周期终止 (EOL)。](https://tomcat.apache.org/tomcat-80-eol.html) 尽管该运行时在 Azure 应用服务上仍然可用，但 Azure 不会为 Tomcat 8.0 应用安全更新。 如果可能，请将你的应用程序迁移到 Tomcat 8.5 或 9.0。 Tomcat 8.5 和 9.0 在 Azure 应用服务上都可用。 有关详细信息，请查看 [Tomcat 官方网站](https://tomcat.apache.org/whichversion.html)。 

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。


### <a name="local-development"></a>本地开发

开发人员可以从 [Azul 下载站点](https://www.azul.com/downloads/azure-only/zulu/)下载 Azul Zulu Enterprise JDK Production Edition 进行本地开发。

### <a name="development-support"></a>开发支持

使用[符合条件的 Azure 支持计划](https://azure.microsoft.com/support/plans/)进行 Azure 或 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 方面的开发时，可以通过 Microsoft 获得对 [Azure 支持的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) 的产品支持。

## <a name="next-steps"></a>后续步骤

请访问[面向 Java 开发人员的 Azure](/java/azure/) 中心查找 Azure 快速入门、教程和 Java 参考文档。

[应用服务 Linux 常见问题解答](faq-app-service-linux.md)中解答了并不特定于 Java 开发的、适用于 Linux 的应用服务的一般用法问题。

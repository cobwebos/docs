---
title: 监视在任何环境中运行的 Java 应用程序 - Azure Monitor Application Insights
description: 使用 Java 独立代理监视在任何环境中运行的 Java 应用程序的应用程序性能，而无需检测该应用。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: b1a23c01fdf8a0145c26b709f36709a7b710103f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374297"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>为 Azure Monitor Application Insights 配置 JVM 参数 Java 独立代理



## <a name="azure-environments"></a>Azure 环境

配置[应用服务](../../app-service/configure-language-java.md#set-java-runtime-options)。

## <a name="spring-boot"></a>Spring Boot

将 JVM 参数 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到 `-jar` 之前的某个位置，例如：

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring Boot（通过 Docker 入口点）

如果使用的是 exec 形式，请将参数 `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` 添加到参数列表中 `"-jar"` 参数之前的某个位置，例如：

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

如果使用的是 shell 形式，请在 `-jar` 之前的某个位置添加 JVM 参数 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`，例如：

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>通过 `apt-get` 或 `yum` 安装的 Tomcat

如果通过 `apt-get` 或 `yum` 安装了 Tomcat，则应该有文件 `/etc/tomcat8/tomcat8.conf`。  将以下行添加到该文件的末尾：

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>通过下载并解压缩相关软件安装的 Tomcat

如果通过从 [https://tomcat.apache.org](https://tomcat.apache.org) 下载并解压缩相关软件安装了 Tomcat，则应有文件 `<tomcat>/bin/catalina.sh` 。  在同一目录中创建包含以下内容的名为 `<tomcat>/bin/setenv.sh` 的新文件：

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果文件 `<tomcat>/bin/setenv.sh` 已存在，则修改该文件并将 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到 `CATALINA_OPTS`。


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>从命令行运行 Tomcat

找到文件 `<tomcat>/bin/catalina.bat`。  在同一目录中创建包含以下内容的名为 `<tomcat>/bin/setenv.bat` 的新文件：

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

引号不是必需的，但如果要包括引号，则正确的位置是：

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果文件 `<tomcat>/bin/setenv.bat` 已存在，只需修改该文件并将 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到 `CATALINA_OPTS` 即可。

### <a name="running-tomcat-as-a-windows-service"></a>将 Tomcat 作为 Windows 服务运行

找到文件 `<tomcat>/bin/tomcat8w.exe`。  运行该可执行文件，并将 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到 `Java` 选项卡下的 `Java Options` 中。


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>独立服务器

将 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到文件 `JBOSS_HOME/bin/standalone.conf` (Linux) 或 `JBOSS_HOME/bin/standalone.conf.bat` (Windows) 中的现有 `JAVA_OPTS` 环境变量：

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>域服务器

将 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到 `JBOSS_HOME/domain/configuration/host.xml` 中的现有 `jvm-options`：

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

如果在单个主机上运行多个托管服务器，则需要将 `applicationinsights.agent.id` 添加到每个 `server` 的 `system-properties`：

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

指定的 `applicationinsights.agent.id` 值必须是独一无二的。 它用于在 applicationinsights 目录下创建一个子目录，因为每个 JVM 进程都需要其自己的本地 applicationinsights 配置和本地 applicationinsights 日志文件。 此外，如果向中央收集器报告，则 `applicationinsights.properties` 文件由多个托管服务器共享，因此需要使用指定的 `applicationinsights.agent.id` 重写该共享文件中的 `agent.id` 设置。 如果需要重写每个托管服务器的 `agent.rollup.id` 设置，则可在服务器的 `system-properties` 中类似地指定 `applicationinsights.agent.rollup.id`。


## <a name="jetty-9"></a>Jetty 9

将以下行添加到 `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

将 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 添加到 `glassfish/domains/domain1/config/domain.xml` 中的现有 `jvm-options`：

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

打开管理控制台，转到“服务器”>“WebSphere 应用程序服务器”>“应用程序服务器”，选择适当的应用程序服务器，然后单击： 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
在“通用 JVM 参数”中，添加以下内容：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
然后，保存并重启应用程序服务器。


## <a name="openliberty-18"></a>OpenLiberty 18

在服务器目录（例如 `<openliberty>/usr/servers/defaultServer`）中创建新文件 `jvm.options`，并添加以下行：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

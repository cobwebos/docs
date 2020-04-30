---
title: 监视在任何环境中运行的 Java 应用程序-Azure Monitor Application Insights
description: 适用于在任何环境中运行的 Java 应用程序的应用程序性能监视，无需对应用进行检测。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641870"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>为 Azure Monitor Application Insights 配置 JVM 参数 Java 独立代理



## <a name="azure-environments"></a>Azure 环境

配置[应用服务](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options)。

## <a name="spring-boot"></a>Spring Boot

在之前`-jar <myapp.jar>`的某个`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`位置添加 JVM 参数，例如：

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> 置于后`-jar <myapp.jar>`的参数作为程序参数传递到应用程序。


## <a name="tomcat-8-linux"></a>Tomcat 8 （Linux）

### <a name="tomcat-installed-via-apt-get-or-yum"></a>通过`apt-get`或安装 Tomcat`yum`

如果通过`apt-get`或`yum`安装了 Tomcat，则应具有文件。 `/etc/tomcat8/tomcat8.conf`  将以下行添加到该文件的末尾：

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>通过下载和解压缩安装的 Tomcat

如果通过下载并从中[https://tomcat.apache.org](https://tomcat.apache.org)解压缩来安装 Tomcat，则应具有文件。 `<tomcat>/bin/catalina.sh`  使用以下内容在名为`<tomcat>/bin/setenv.sh`的同一目录中创建新文件：

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果文件`<tomcat>/bin/setenv.sh`已存在，则修改该文件并将添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`到`CATALINA_OPTS`。


## <a name="tomcat-8-windows"></a>Tomcat 8 （Windows）

### <a name="running-tomcat-from-the-command-line"></a>从命令行运行 Tomcat

找到该文件`<tomcat>/bin/catalina.bat`。  使用以下内容在名为`<tomcat>/bin/setenv.bat`的同一目录中创建新文件：

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

引号并不是必需的，但如果要包含它们，正确的位置是：

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果文件`<tomcat>/bin/setenv.bat`已存在，只需修改该文件并将`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`其`CATALINA_OPTS`添加到。

### <a name="running-tomcat-as-a-windows-service"></a>将 Tomcat 作为 Windows 服务运行

找到该文件`<tomcat>/bin/tomcat8w.exe`。  运行该可执行文件`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`并将`Java Options`添加到`Java`选项卡下的。


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>独立服务器

添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`到文件`JAVA_OPTS` `JBOSS_HOME/bin/standalone.conf` （Linux）或`JBOSS_HOME/bin/standalone.conf.bat` （Windows）中的现有环境变量：

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>域服务器

添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`到中`JBOSS_HOME/domain/configuration/host.xml`的`jvm-options`现有：

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

如果在单个主机上运行多个托管服务器，则需要为每个`applicationinsights.agent.id` `system-properties` `server`服务器添加一个：

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

指定`applicationinsights.agent.id`的值必须是唯一的。 它用于在 applicationinsights.config 目录下创建子目录，因为每个 JVM 进程都需要其自己的本地 applicationinsights.config 配置和本地 applicationinsights.config 日志文件。 此外，如果向中央收集器报告，该`applicationinsights.properties`文件将由多个托管服务器共享，因此需要指定`applicationinsights.agent.id`该共享文件中的`agent.id`设置。 `applicationinsights.agent.rollup.id`如果需要重写每个托管服务器`system-properties`的`agent.rollup.id`设置，则在服务器的中可以采用类似的方式指定。


## <a name="jetty-9"></a>Jetty 9

将这些行添加到`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`到中`glassfish/domains/domain1/config/domain.xml`的`jvm-options`现有：

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

打开管理控制台中转到 **> WebSphere 应用程序服务器的服务器 > 应用程序服务器**，选择适当的应用程序服务器，然后单击： 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
在 "通用 JVM 参数" 中，添加以下内容：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
然后，保存并重新启动应用程序服务器。


## <a name="openliberty-18"></a>OpenLiberty 18

在服务器目录中`jvm.options`创建新文件（例如`<openliberty>/usr/servers/defaultServer`），并添加以下行：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

---
title: 监视在任何环境中运行的 Java 应用程序 - Azure 监视器应用程序见解
description: 应用程序性能监视，适用于使用 Java 独立代理在任何环境中运行的 Java 应用程序，而无需检测应用程序。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641870"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>为 Azure 监视器应用程序见解配置 JVM args Java 独立代理



## <a name="azure-environments"></a>Azure 环境

配置[应用服务](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options)。

## <a name="spring-boot"></a>Spring Boot

在之前`-jar <myapp.jar>`的某个地方添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`JVM arg，例如：

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> 之后放置的`-jar <myapp.jar>`Args 作为程序 args 传递给应用程序。


## <a name="tomcat-8-linux"></a>汤姆卡特 8 （Linux）

### <a name="tomcat-installed-via-apt-get-or-yum"></a>通过`apt-get`或`yum`

如果您通过`apt-get`或`yum`（） 安装了 Tomcat，则应`/etc/tomcat8/tomcat8.conf`有一个文件。  将此行添加到该文件的末尾：

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>通过下载和解压缩安装的 Tomcat

如果你通过下载和解压缩安装[https://tomcat.apache.org](https://tomcat.apache.org)Tomcat，那么你应该有一个文件。 `<tomcat>/bin/catalina.sh`  在同一目录`<tomcat>/bin/setenv.sh`中创建一个包含以下内容的新文件：

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果文件`<tomcat>/bin/setenv.sh`已存在，则修改该文件并添加到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar``CATALINA_OPTS`。


## <a name="tomcat-8-windows"></a>汤姆卡特 8 （窗口）

### <a name="running-tomcat-from-the-command-line"></a>从命令行运行 Tomcat

找到文件`<tomcat>/bin/catalina.bat`。  在同一目录`<tomcat>/bin/setenv.bat`中创建一个包含以下内容的新文件：

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

报价不是必需的，但如果要包括报价，正确的放置位置是：

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果文件`<tomcat>/bin/setenv.bat`已存在，只需修改该文件并添加到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar``CATALINA_OPTS`。

### <a name="running-tomcat-as-a-windows-service"></a>将 Tomcat 作为 Windows 服务运行

找到文件`<tomcat>/bin/tomcat8w.exe`。  运行该可执行文件并`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`添加到选项卡`Java Options`下。 `Java`


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>独立服务器

添加到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`文件`JAVA_OPTS``JBOSS_HOME/bin/standalone.conf`（Linux） 或`JBOSS_HOME/bin/standalone.conf.bat`（Windows） 中的现有环境变量：

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>域服务器

添加到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`中的现有`jvm-options` `JBOSS_HOME/domain/configuration/host.xml`：

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

如果在单个主机上运行多个托管服务器，则需要为每个 服务器添加`applicationinsights.agent.id`到 ： `system-properties` `server`

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

指定的`applicationinsights.agent.id`值必须是唯一的。 它用于在应用程序见解目录下创建子目录，因为每个 JVM 进程都需要其自己的本地应用程序见解配置和本地应用程序见解日志文件。 此外，如果向中央收集器报告，`applicationinsights.properties`该文件将由多个托管服务器共享，因此需要指定`applicationinsights.agent.id`来覆盖该共享文件中的`agent.id`设置。 `applicationinsights.agent.rollup.id``system-properties`如果需要覆盖每个托管服务器的设置，`agent.rollup.id`可以在服务器中同样指定。


## <a name="jetty-9"></a>码头 9

将这些行添加到`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>帕亚拉 5

添加到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`中的现有`jvm-options` `glassfish/domains/domain1/config/domain.xml`：

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

## <a name="websphere-8"></a>网络圈 8

开放管理控制台转到**WebSphere 应用程序服务器>应用程序服务器>服务器**，选择适当的应用程序服务器，然后单击： 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
在"通用 JVM 参数"中添加以下内容：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
之后，保存并重新启动应用程序服务器。


## <a name="openliberty-18"></a>开放自由 18

在服务器目录中创建新`jvm.options`文件（例如`<openliberty>/usr/servers/defaultServer`），然后添加此行：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

---
title: Linux 上的 Java 企业支持 - Azure 应用服务 | Microsoft Docs
description: 在 Linux 上使用 Wildfly 和 Azure 应用服务部署 Java 企业应用的开发人员指南。
keywords: azure 应用服务、 web 应用、 linux、 os、 java、 wildfly、 企业和 java ee jee，javaee
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
ms.custom: seodec18
ms.openlocfilehash: 98e00eb382962d2364adda93b1a5b24fdef832eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101765"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Linux 上应用服务的 Java 企业指南

> [!NOTE] 
> 应用服务 Linux 上的 Java 企业版目前处于预览状态。 此堆栈**不**建议用于面向生产的工作。 请参阅[Java 开发人员指南](app-service-linux-java.md)我们 Java SE 和 Tomcat 在堆栈上的信息。

Linux 上的 azure 应用服务允许 Java 开发人员能够生成、 部署和缩放 Java 企业 (Java EE) 应用程序上完全托管的基于 Linux 的服务。  基础 Java 企业运行时环境是开源 [Wildfly](https://wildfly.org/) 应用程序服务器。

本指南提供 Java 企业开发人员可在适用于 Linux 的应用服务中使用的重要概念和说明。 如果从未使用适用于 Linux 的 Azure 应用服务部署 Java 应用程序，则应先完成 [Java 快速入门](quickstart-java.md)。 有关 Java 应用的应用服务的问题，请参阅 [Java 开发人员指南](app-service-linux-java.md)和 [Linux 应用服务常见问题解答](app-service-linux-faq.md)。

## <a name="scale-with-app-service"></a>应用服务缩放 

在 Linux 上的应用服务中运行的 WildFly 应用程序服务器以独立模式运行，而不是在域配置中运行。 横向扩展应用服务计划时，每个 WildFly 实例都被配置为独立的服务器。

 使用[缩放规则](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json)和[增加实例数](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，纵向或横向缩放应用程序。 

## <a name="customize-application-server-configuration"></a>自定义应用程序服务器配置

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

将启动脚本上传到应用服务实例中的 `/home/site/deployments/tools`。 有关获取 FTP 凭据的说明，请参阅[本文档](/azure/app-service/deploy-configure-credentials#userscope)。 

将 Azure 门户中的“启动脚本”字段设置为启动 shell 脚本的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。

在应用程序配置中提供[应用程序设置](/azure/app-service/web-sites-configure#application-settings)，以传递供在脚本中使用的环境变量。 应用程序设置将连接字符串和配置应用程序所需的其他机密置于版本控制之外。

## <a name="modules-and-dependencies"></a>模块和依赖项

要通过 JBoss CLI 将模块及其依赖项安装到 Wildfly 类路径中，需要在其自己的目录中创建以下文件。  某些模块和依赖项可能需要其他配置，例如 JNDI 命名或其他特定于 API 的配置，因此此列表是在大多数情况下配置依赖项所需的最小集。

- [XML 模块描述符](https://jboss-modules.github.io/jboss-modules/manual/#descriptors)。 此 XML 文件定义模块的名称、属性和依赖项。 此[示例 module.xml 文件](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)定义了 Postgres 模块、其 JAR 文件 JDBC 依赖项以及所需的其他模块依赖项。
- 模块的任何必要 JAR 文件依赖项。
- 具有 JBoss CLI 命令的脚本，用于配置新模块。 该文件将包含 JBoss CLI 要执行的命令，以配置服务器使用依赖项。 有关添加模块、数据源和消息提供程序的命令的文档，请参阅[本文档](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)。
-  用于调用 JBoss CLI 和执行上一步中脚本的 Bash 启动脚本。 重启应用服务实例或在横向扩展期间配置新实例时，将执行此文件。当 JBoss 命令传递给 JBoss CLI 时，可在此启动脚本中为应用程序执行任何其他配置。 此文件可以是将 JBoss CLI 命令脚本传递给 JBoss CLI 的最小单个命令： 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

获得模块的文件和内容后，请按照以下步骤将模块添加到 Wildfly 应用程序服务器。 

1. 将文件 FTP 到应用服务实例中的 `/home/site/deployments/tools`。 有关获取 FTP 凭据的说明，请参阅本文档。 
2. 在 Azure 门户的“应用程序设置”边栏选项卡中，将“启动脚本”字段设置为启动 shell 脚本的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。
3. 通过按下门户网站“概述”部分中的“重启”按钮或使用 Azure CLI 重启应用服务实例。

## <a name="data-sources"></a>数据源

要为数据源连接配置 Wildfly，请按照上面“安装模块和依赖项”部分中概述的相同过程进行操作。 可对任何 Azure 数据库服务执行相同的步骤。

1. 下载适用于数据库风格的 JDBC 驱动程序。 为方便起见，以下是 [Postgres](https://jdbc.postgresql.org/download.html) 和 [MySQL](https://dev.mysql.com/downloads/connector/j/) 的驱动程序。 解压缩下载项以获取 .jar 文件。
2. 按照“模块和依赖项”中概述的步骤创建和上传 XML 模块描述符、JBoss CLI 脚本、启动脚本和 JDBC .jar 依赖项。


有关使用 [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)、[MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) 和 [SQL 数据库](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)配置 Wildfly 的详细信息已可供参阅。 可使用这些自定义说明以及上述通用方法将数据源定义添加到服务器。

## <a name="messaging-providers"></a>消息提供程序

要使用服务总线作为消息传递机制启用消息驱动 Bean，请执行以下操作：

1. 使用 [Apache QPId JMS 消息传递库](https://qpid.apache.org/proton/index.html)。 在应用程序的 pom.xml（或其他生成文件）中包含此依赖项。

2.  创建[服务总线资源](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)。 在该命名空间内创建 Azure 服务总线命名空间和队列，以及具有发送和接收功能的共享访问策略。

3. 通过对策略的主密钥进行 URL 编码或[使用服务总线 SDK ](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)，将共享访问策略密钥传递给代码。

4. 按照安装模块和依赖关系部分中概述的步骤，使用模块 XML 描述符、.jar依赖项、JBoss CLI 命令和 JMS 提供程序的启动脚本。 除这四个文件外，还需要创建一个 XML 文件，该文件定义 JMS 队列和主题的 JNDI 名称。 请参阅[此存储库](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)，获取有关参考配置文件。


## <a name="configure-session-management-caching"></a>配置会话管理缓存

默认情况下，Linux 上的应用服务将使用会话相关性 cookie 来确保将现有会话的客户端请求路由到应用程序的同一实例。 此默认行为不需要配置，但存在一些限制：

- 如果重启或减少应用程序实例，应用程序服务器中的用户会话状态将会丢失。
- 如果应用程序具有较长的会话超时设置或固定数量的用户，则自动调整的新实例可能需要一些时间才能接收负载，因为只有新会话将路由到新启动的实例。

可将 Wildfly 配置为使用外部会话存储，例如 [Azure Redis 缓存](/azure/azure-cache-for-redis/)。 需要[禁用现有 ARR 实例相关性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)配置，才能关闭基于会话的 cookie 路由，以及允许配置的 Wildfly 会话存储运行不受干扰。

## <a name="enable-web-sockets"></a>启用 Web 套接字

默认情况下，应用服务上已启用 Web 套接字。 要在应用程序中使用 WebSockets，请参阅[本快速入门](https://github.com/wildfly/quickstart/tree/master/websocket-hello)。

## <a name="logs-and-troubleshooting"></a>日志和疑难解答

应用服务提供的工具有助于解决应用程序的问题。

-   通过单击左侧导航窗格中的“诊断日志”启用日志记录。 单击“文件系统”，设置存储配额和保持期，然后保存更改。 可在 `/home/LogFiles/` 下找到这些日志。
-   [使用 SSH 连接到应用程序实例](app-service-linux-ssh-support.md)，查看所运行应用程序的日志。
-   检查门户“诊断日志”窗格中的诊断日志，或使用 Azure CLI 命令：` az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group> `

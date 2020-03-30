---
title: 实时流式传输 Azure Spring Cloud 应用日志
description: 如何使用日志流即时查看应用程序日志
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192194"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>实时流式传输 Azure Spring Cloud 应用日志
Azure Spring Cloud 使 Azure CLI 中的日志流获取用于故障排除的实时应用程序控制台日志。 您还可以[使用诊断设置分析日志和指标](./diagnostic-services.md)。

## <a name="prerequisites"></a>先决条件

* 安装春云的[Azure CLI 扩展，](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension)最小版本 0.2.0 。
* 具有正在运行的应用程序的**Azure 春云**实例，例如[Spring Cloud 应用](./spring-cloud-quickstart-launch-app-cli.md)。

> [!NOTE]
>  ASC CLI 扩展从版本 0.2.0 更新为 0.2.1。 此更改会影响日志流命令的语法： `az spring-cloud app log tail`， 替换为 ： `az spring-cloud app logs`。 命令：`az spring-cloud app log tail`将在将来的版本中弃用。 如果您一直在使用版本 0.2.0，则可以升级到 0.2.1。 首先，使用命令删除旧版本： `az extension remove -n spring-cloud`。  然后，按命令安装 0.2.1： `az extension add -n spring-cloud`。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 跟踪日志

为避免重复指定资源组和服务实例名称，请设置默认资源组名称和群集名称。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
在以下示例中，将在命令中省略资源组和服务名称。

### <a name="tail-log-for-app-with-single-instance"></a>具有单个实例的应用的尾日志
如果名为 auth-service 的应用只有一个实例，则可以使用以下命令查看应用实例的日志：
```
az spring-cloud app logs -n auth-service
```
这将返回日志：
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>具有多个实例的应用的尾日志
如果名为`auth-service`的应用存在多个实例，则可以使用 选项`-i/--instance`查看实例日志。 

首先，可以使用以下命令获取应用实例名称。

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
结果：

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
然后，您可以使用选项`-i/--instance`流应用实例的日志：

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

您还可以从 Azure 门户获取应用实例的详细信息。  在 Azure Spring 云服务服务的左侧导航窗格中选择**应用**后，选择**应用实例**。

### <a name="continuously-stream-new-logs"></a>持续流式传输新日志
默认情况下，`az spring-cloud ap log tail`仅打印流式传输到应用控制台的现有日志，然后退出。 如果要流式传输新日志，请添加 -f （-关注）：  

```
az spring-cloud app logs -n auth-service -f
``` 
要检查所有支持的日志记录选项，请进行以下操作：
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>后续步骤

* [使用诊断设置分析日志和指标](./diagnostic-services.md)

 






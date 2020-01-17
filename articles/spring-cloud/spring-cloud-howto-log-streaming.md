---
title: 实时流式传输 Azure 春季云应用日志
description: 如何使用日志流式处理即时查看应用程序日志
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156414"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>实时流式传输 Azure 春季云应用日志
Azure 春季 Cloud 在 Azure CLI 中启用日志流式处理，以获取实时应用程序控制台日志以进行故障排除。 还可以[通过诊断设置分析日志和指标](./diagnostic-services.md)。

## <a name="prerequisites"></a>必备组件

* 安装适用于春季 Cloud 的[Azure CLI 扩展](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension)，最低版本为0.2.0。
* 带有正在运行的应用程序的**Azure 春季 cloud**的实例，例如 "[春季 cloud app](./spring-cloud-quickstart-launch-app-cli.md)"。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 来结尾日志

若要避免重复指定资源组和服务实例的名称，请设置默认资源组名称和群集名称。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
在以下示例中，将在命令中省略资源组和服务名称。

### <a name="tail-log-for-app-with-single-instance"></a>带有单个实例的应用程序的尾日志
如果名为 "身份验证服务" 的应用只有一个实例，则可以使用以下命令查看应用实例的日志：
```
az spring-cloud app log tail -n auth-service
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

### <a name="tail-log-for-app-with-multiple-instances"></a>具有多个实例的应用的结尾日志
如果名为 `auth-service`的应用存在多个实例，则可以使用 `-i/--instance` 选项查看实例日志。 例如，可以通过指定应用名称和实例名称来流式传输一个应用的实例的日志：

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
还可以从 Azure 门户获取应用实例。 
1. 导航到资源组，并选择 Azure 春季云实例。
1. 从 Azure 春季 Cloud 实例概述的左侧导航窗格中选择 "**应用**"。
1. 选择应用，然后单击左侧导航窗格中的 "**应用实例**"。 
1. 将显示应用程序实例。

### <a name="continuously-stream-new-logs"></a>连续流式传输新日志
默认情况下，`az spring-cloud ap log tail` 仅打印流式传输到应用控制台的现有日志，然后退出。 如果要流式传输新日志，请添加-f （--跟随）：  

```
az spring-cloud app log tail -n auth-service -f
``` 
检查支持的所有日志记录选项：
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>后续步骤

* [通过诊断设置分析日志和指标](./diagnostic-services.md)

 






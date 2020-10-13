---
title: Azure Application Insights - 依赖项自动收集 | Microsoft Docs
description: Application Insights 自动收集和可视化依赖项
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: 8a4d79e52465e93fb4db2625217cb37a06917218
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930860"
---
# <a name="dependency-auto-collection"></a>依赖项自动收集

下面是当前支持的依赖项调用的列表，系统会自动将其检测为依赖项，不需对应用程序的代码进行额外的修改。 这些依赖项在 Application Insights 的[应用程序映射](./app-map.md)和[事务诊断](./transaction-diagnostics.md)视图中可视化。 如果依赖项不在下面的列表中，仍可通过[跟踪依赖项调用](./api-custom-events-metrics.md#trackdependency)手动跟踪它。

## <a name="net"></a>.NET

| 应用框架| 版本 |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> 通信库</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+、.NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+、NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 - 最新稳定版本。 （请参阅下面的说明。）
| [EventHubs 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>存储客户端</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> 早期版本的 Microsoft.Data.SqlClient 存在一个[已知问题](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347)。 建议使用 1.1.0 或更高版本来缓解此问题。 Entity Framework Core 不一定随附 Microsoft.Data.SqlClient 的最新稳定版本，因此我们建议你确认所使用的最低版本是 1.1.0 版本以避免此问题。   


## <a name="java"></a>Java
| 应用服务器 | 版本 |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6、7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>应用框架</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>通信库</b> |  |
| [Apache Http 客户端](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>存储客户端</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL（Beta 版本支持）](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>日志记录库</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>指标库</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *响应式编程支持除外。
> <br>†需安装 [JVM 代理](./java-agent.md#install-the-application-insights-agent-for-java)。

## <a name="nodejs"></a>Node.js

| 通信库 | 版本 |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html)、[HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>存储客户端</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb)；[MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg)； | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>日志记录库</b> | |
| [console](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>Javascript

| 通信库 | 版本 |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | 全部 |

## <a name="next-steps"></a>后续步骤

- 为 [.NET](./asp-net-dependencies.md) 设置自定义依赖项跟踪。
- 为 [Java](./java-agent.md) 设置自定义依赖项跟踪。
- 为 [OpenCensus Python](./opencensus-python-dependency.md) 设置自定义依赖项跟踪。
- [编写自定义依赖项遥测](./api-custom-events-metrics.md#trackdependency)
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](./data-model.md)。
- 查看 Application Insights 支持的[平台](./platforms.md)。


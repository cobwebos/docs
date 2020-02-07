---
title: Azure Application Insights - 依赖项自动收集 | Microsoft Docs
description: Application Insights 自动收集和可视化依赖项
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e774690fae2314b8db6e14f19a67c7ae1ce3a203
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064268"
---
# <a name="dependency-auto-collection"></a>依赖项自动收集

下面是当前支持的依赖项调用的列表，系统会自动将其检测为依赖项，不需对应用程序的代码进行额外的修改。 这些依赖项在 Application Insights 的[应用程序映射](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)和[事务诊断](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics)视图中可视化。 如果依赖项不在下面的列表中，仍可通过[跟踪依赖项调用](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)手动跟踪它。

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
| [EventHubs 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>存储客户端</b>|  |
| ADO.NET | 4.5+ |

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
| [PostgreSQL （测试版支持）](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>日志记录库</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>指标库</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *响应式编程支持除外。
> <br>†需安装 [JVM 代理](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)。

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

## <a name="javascript"></a>JavaScript

| 通信库 | 版本 |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | 全部 |

## <a name="next-steps"></a>后续步骤

- 为 [.NET](../../azure-monitor/app/asp-net-dependencies.md) 设置自定义依赖项跟踪。
- 为 [Java](../../azure-monitor/app/java-agent.md) 设置自定义依赖项跟踪。
- 为[OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md)设置自定义依赖项跟踪。
- [编写自定义依赖项遥测](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](../../azure-monitor/app/data-model.md)。
- 查看 Application Insights 支持的[平台](../../azure-monitor/app/platforms.md)。

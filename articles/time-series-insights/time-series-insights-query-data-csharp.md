---
title: 使用C#代码从 GA 环境中查询数据-Azure 时序见解 |Microsoft Docs
description: 了解如何使用以编写的自定义应用查询 Azure 时序见解环境中的数据C#。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987965"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>使用从 Azure 时序见解 GA 环境查询数据C#

此C#示例演示如何使用[GA 查询 Api](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)从 Azure 时序见解 GA 环境中查询数据。

> [!TIP]
> 查看C# GA 代码示例[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)。

## <a name="summary"></a>Summary

下面的示例代码演示了以下功能：

* 如何通过 Azure Active Directory 使用[system.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)获取访问令牌。

* 如何在后续查询 API 请求的 `Authorization` 标头中传递获取的访问令牌。 

* 此示例调用每个 GA 查询 Api，其中演示了如何对执行 HTTP 请求：
    * [获取环境 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api)以返回用户有权访问的环境
    * [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [获取环境元数据 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api)以检索环境元数据
    * [获取环境事件 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [获取环境聚合 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* 如何使用 WSS 与 GA 查询 Api 进行交互以发送消息：

   * [获取环境事件流式处理 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [获取环境聚合流 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>先决条件和设置

在编译和运行示例代码之前，请完成以下步骤：

1. [预配 GA Azure 时序见解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)环境。
1. 根据[身份验证和授权](time-series-insights-authentication-and-authorization.md)中所述，配置适用于 Azure Active Directory 的 Azure 时序见解环境。 
1. 安装所需的项目依赖项。
1. 通过将每个 **#DUMMY #** 替换为适当的环境标识符，编辑下面的示例代码。
1. 在 Visual Studio 中执行该代码。

## <a name="project-dependencies"></a>项目依赖项

建议使用最新版本的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -版本 16.4.2 +

示例代码包含两个必需的依赖项：

* [System.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9 包。
* [Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1 包。

通过选择 "**生成** > **生成解决方案**" 选项，在 Visual Studio 2019 中下载包。

或者，使用[NuGet 2.12 +](https://www.nuget.org/)添加包：

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 示例代码

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>后续步骤

- 若要了解有关查询的详细信息，请参阅[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 了解如何[使用客户端 SDK 将 JavaScript 应用程序连接](https://github.com/microsoft/tsiclient)到时序见解。

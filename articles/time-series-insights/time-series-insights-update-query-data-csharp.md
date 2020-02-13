---
title: 使用C# Azure 时序见解从预览版环境中查询数据 |Microsoft Docs
description: 了解如何使用编写的应用从 Azure 时序见解环境中查询数据C#。
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980981"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用从 Azure 时序见解预览环境查询数据C#

此C#示例演示如何在 Azure 时序见解预览版环境中从[预览版数据访问 api](https://docs.microsoft.com/rest/api/time-series-insights/preview)查询数据。

> [!TIP]
> 查看C#预览代码示例[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)。

## <a name="summary"></a>Summary

下面的示例代码演示了以下功能：

* 支持从[Azure AutoRest](https://github.com/Azure/AutoRest)自动生成 SDK。
* 如何通过 Azure Active Directory 使用[system.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)获取访问令牌。
* 如何在后续数据访问 API 请求的 `Authorization` 标头中传递获取的访问令牌。 
* 该示例提供了一个控制台界面，其中演示了如何对发出 HTTP 请求：

    * [预览环境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [获取环境可用性 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)并[获取事件架构 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [预览查询 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [获取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)、[获取序列 Api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)和[获取聚合序列 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [时序模型 Api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [获取层次结构 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get)和[层次结构批处理 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [获取类型 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)和[类型批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [获取实例 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get)和[实例批处理 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 高级[搜索](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features)和[TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)功能。

## <a name="prerequisites-and-setup"></a>先决条件和设置

在编译和运行示例代码之前，请完成以下步骤：

1. [预配预览版 Azure 时序见解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)环境。
1. 根据[身份验证和授权](time-series-insights-authentication-and-authorization.md)中所述，配置适用于 Azure Active Directory 的 Azure 时序见解环境。 
1. 运行[Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md)中指定的[GenerateCode](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) ，以生成时序见解预览客户端依赖项。
1. 打开 `TSIPreviewDataPlaneclient.sln` 解决方案，并将 `DataPlaneClientSampleApp` 设置为 Visual Studio 中的默认项目。
1. 使用[如下](#project-dependencies)所述的步骤安装所需的项目依赖项，并将该示例编译为可执行文件 `.exe` 文件。
1. 双击 `.exe` 文件以运行该文件。

## <a name="project-dependencies"></a>项目依赖项

建议使用最新版本的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -版本 16.4.2 +

示例代码具有几个必需的依赖项，这些依赖项可以在[包 .config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)文件中查看。

通过选择 "**生成** > **生成解决方案**" 选项，在 Visual Studio 2019 中下载包。 

或者，使用[NuGet 2.12 +](https://www.nuget.org/)添加每个包。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 示例代码

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 可以在不更改默认环境变量的情况下执行代码示例。
> * 此代码示例将编译为 .NET 可执行控制台应用。

## <a name="next-steps"></a>后续步骤

- 若要了解有关查询的详细信息，请参阅[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)。

- 了解如何[使用客户端 SDK 将 JavaScript 应用程序连接](https://github.com/microsoft/tsiclient)到时序见解。

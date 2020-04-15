---
title: 使用 C# 从预览版环境查询数据 - Azure 时序见解 | Microsoft Docs
description: 了解如何使用 C# 编写的应用从 Azure 时序见解环境查询数据。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379815"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用 C# 查询 Azure 时序见解预览版环境中的数据

此 C# 示例演示如何在 Azure 时间序列预览环境中查询预览[数据访问 API](https://docs.microsoft.com/rest/api/time-series-insights/preview)中的数据。

> [!TIP]
> 在 上[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)查看预览 C# 代码示例。

## <a name="summary"></a>总结

下面的示例代码演示了以下功能：

* 支持从 Azure 自动[Rest](https://github.com/Azure/AutoRest)自动生成 SDK。
* 如何使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 通过 Azure Active Directory 获取访问令牌。
* 如何在后续数据访问 API 请求的`Authorization`标头中传递获取的访问令牌。 
* 该示例提供了一个控制台界面，演示如何向 以下项发出 HTTP 请求：

    * [预览环境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)并[获取事件架构 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [预览查询 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [获取事件 API、](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)[获取系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)和[获取聚合系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [时间序列模型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [获取层次结构 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get)和[层次结构批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [获取类型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)和[类型批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [获取实例 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get)和[实例批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 高级[搜索](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features)和[TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)功能。

## <a name="prerequisites-and-setup"></a>先决条件和设置

在编译和运行示例代码之前，请完成以下步骤：

1. [预配预览 Azure 时间序列见解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)环境。
1. 为 Azure Active Directory 配置 Azure 时序见解环境，如[身份验证和授权](time-series-insights-authentication-and-authorization.md)中所述。 
1. 运行[Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md)中指定的[GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat)以生成时间序列见解预览客户端依赖项。
1. 打开解决方案`TSIPreviewDataPlaneclient.sln`并将其设置为`DataPlaneClientSampleApp`Visual Studio 中的默认项目。
1. 使用[下面](#project-dependencies)描述的步骤安装所需的项目依赖项，并将示例编译为可`.exe`执行文件。
1. 双击`.exe`文件即可运行该文件。

## <a name="project-dependencies"></a>项目依赖项

建议使用最新版 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2+

示例代码具有几个必需的依赖项，可以在[包](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)中查看。

通过选择 **"生成生成** > **解决方案**"选项，下载 Visual Studio 2019 中的软件包。 

或者，使用[NuGet 2.12+](https://www.nuget.org/)添加每个包。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 示例代码

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 可以在不更改默认环境变量的情况下执行代码示例。
> * 代码示例将编译到 .NET 可执行控制台应用。

## <a name="next-steps"></a>后续步骤

- 若要详细了解查询，请阅读[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)。

- 阅读如何[使用客户端 SDK 将 JavaScript 应用连接到时序见解](https://github.com/microsoft/tsiclient)。

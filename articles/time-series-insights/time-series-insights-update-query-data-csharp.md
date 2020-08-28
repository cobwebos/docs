---
title: 使用 C# 查询 Gen2 环境中的数据 - Azure 时序见解 | Microsoft Docs
description: 了解如何通过使用 C# 编写的应用查询 Azure 时序见解 Gen2 环境中的数据。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: fcb38f761a72bf2c6e5dee8bb993cec9143e7241
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998945"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>使用 C 升查询 Azure 时序见解 Gen2 环境中的数据

本 C# 示例演示了如何在 Azure 时序见解 Gen2 环境中通过 [Gen2 数据访问 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) 查询数据。

> [!TIP]
> 可以访问 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) 查看 Gen2 C# 代码示例。

## <a name="summary"></a>总结

下面的示例代码演示了以下功能：

* 支持通过 [Azure AutoRest](https://github.com/Azure/AutoRest) 自动生成 SDK。
* 如何使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 通过 Azure Active Directory 获取访问令牌。
* 如何在后续的数据访问 API 请求的 `Authorization` 标头中传递获取的访问令牌。
* 该示例提供了一个控制台界面，演示如何对以下内容进行 HTTP 请求：
  * [Gen2 环境 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) 和[获取事件架构 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2 查询 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * [获取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)、[获取时序 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) 和[获取聚合时序 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [时序模型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [获取层次结构 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) 和[层次结构批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [获取类型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) 和[类型批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [获取实例 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) 和[实例批处理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* 高级[搜索](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features)和 [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 功能。

## <a name="prerequisites-and-setup"></a>先决条件和设置

在编译和运行示例代码之前，请完成以下步骤：

1. [预配 Azure 时序见解 Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) 环境。
1. 为 Azure Active Directory 配置 Azure 时序见解环境，如[身份验证和授权](time-series-insights-authentication-and-authorization.md)中所述。
1. 按照 [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) 中指定的方式运行 [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat)，以生成 Azure 时序见解 Gen2 客户端依赖项。
1. 打开 `TSIPreviewDataPlaneclient.sln` 解决方案，并将 `DataPlaneClientSampleApp` 设置为 Visual Studio 中的默认项目。
1. 使用[下面](#project-dependencies)所述的步骤安装所需的项目依赖项，并将示例编译为可执行 `.exe` 文件。
1. 通过双击 `.exe` 文件来运行它。

## <a name="project-dependencies"></a>项目依赖项

建议使用最新版本的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2+

示例代码具有多个必需的依赖项，可以在 [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) 文件中进行查看。

在 Visual Studio 2019 中，通过选择“生成” > “生成解决方案”选项下载包。

或者，使用 [NuGet 2.12 ](https://www.nuget.org/) 添加每个包。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 示例代码

请参阅 [Azure 时序见解存储库](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) ，以访问 c # 示例代码。 

> [!NOTE]
>
> * 可以在不更改默认环境变量的情况下执行代码示例。
> * 代码示例将编译为 .NET 可执行控制台应用。

## <a name="next-steps"></a>后续步骤

* 若要了解有关查询的详细信息，请参阅[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)。

* 阅读如何[使用客户端 SDK 将 JavaScript 应用连接到 Azure 时序见解](https://github.com/microsoft/tsiclient)。

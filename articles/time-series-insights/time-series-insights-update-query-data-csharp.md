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
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 20c1f1f9a8b0b0ef105893e44c9daaeae68604db
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889747"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用从 Azure 时序见解预览环境查询数据C#

此C#示例演示如何从 Azure 时序见解预览环境查询数据。

该示例演示了多个基本的查询 API 使用示例：

1. 准备时，通过 Azure Active Directory API 获取访问令牌。 在每个查询 API 请求的 `Authorization` 标头中传递此令牌。 有关如何设置非交互式应用程序，请参阅[身份验证和授权](time-series-insights-authentication-and-authorization.md)。 此外，请确保正确设置此示例开头定义的所有常量。
1. 已获得该用户有权访问的环境的列表。 将选取一个环境作为感兴趣的环境，并会查询该环境的更多数据。
1. 以 HTTPS 请求为例，可以为感兴趣的环境请求可用性数据。
1. 提供来自[Azure AutoRest](https://github.com/Azure/AutoRest)的 SDK 自动生成支持的示例。

> [!NOTE]
> [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)提供了示例代码以及编译和运行该代码的步骤。

## <a name="c-example"></a>C# 示例

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> 上面的代码示例可以在不更改默认环境值的情况下运行。

## <a name="next-steps"></a>后续步骤

- 若要了解有关查询的详细信息，请参阅[查询 API 参考](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)。

- 了解如何[使用客户端 SDK 将 JavaScript 应用程序连接](https://github.com/microsoft/tsiclient)到时序见解。

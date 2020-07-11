---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169896"
---
| | |
|--|--|
|**`<DESTINATION>`**| 日志要发送到的目标。 有效值为 `AppInsights` 和 `Blob`。<br/>使用时 `AppInsights` ，请确保[在 function app 中启用 Application Insights](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration)。<br/>将目标设置为时 `Blob` ，会在 `azure-functions-scale-controller` 应用程序设置中设置的默认存储帐户中指定的 blob 容器中创建日志 `AzureWebJobsStorage` 。 |
|**`<VERBOSITY>`** | 指定日志记录级别。 支持的值为 `None`、`Warning` 和 `Verbose`。<br/>当设置为时 `Verbose` ，缩放控制器记录辅助角色计数中每次更改的原因，以及有关构成这些决策的触发器的信息。 详细日志包含触发器警告和缩放控制器运行前后触发器使用的哈希。 |

> [!CAUTION]
> 不要让扩展控制器日志记录处于启用状态。 启用日志记录，直到收集了足够的数据来了解规模控制器的行为方式，然后再将其禁用。
---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 31a0acbfd0c9714bf2cbdf20f9f1f82edb07f05a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977333"
---
此 `ApplicationInsights` 设置允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。 Application Insights 可深入监视容器。 可以轻松监视容器的可用性、性能和使用情况。 还可以快速识别和诊断容器中的错误。

下表描述了 `ApplicationInsights` 节支持的配置设置。

|必选| 名称 | 数据类型 | Description |
|--|------|-----------|-------------|
|否| `InstrumentationKey` | String | 容器遥测数据要发送到的 Application Insights 实例的检测密钥。 有关详细信息，请参阅[适用于 ASP.NET Core 的 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)。 <br><br>示例：<br>`InstrumentationKey=123456789`|


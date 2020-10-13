---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711159"
---
Azure 存储在 Azure Monitor 中提供以下事务指标。

| 指标 | 说明 |
| ------------------- | ----------------- |
| 事务 | 向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求数，以及引发错误的请求数。 <br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 适用的维度：ResponseType、GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)）<br/> 值示例：1024 |
| 流入量 | 流入数据量。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 <br/><br/> 单位：字节 <br/> 聚合类型：总计 <br/> 适用的维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| 流出量 | 流出数据量。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。 <br/><br/> 单位：字节 <br/> 聚合类型：总计 <br/> 适用的维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| SuccessServerLatency | Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。 <br/><br/> 单位：毫秒 <br/> 聚合类型：平均值 <br/> 适用的维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| SuccessE2ELatency | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 <br/><br/> 单位：毫秒 <br/> 聚合类型：平均值 <br/> 适用的维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| 可用性 | 存储服务或指定的 API 操作的可用性百分比。 可用性通过由“计费请求总数”值除以适用的请求数（包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 <br/><br/> 单位：百分比 <br/> 聚合类型：平均值 <br/> 适用的维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：99.99 |
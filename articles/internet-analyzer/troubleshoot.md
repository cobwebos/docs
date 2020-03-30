---
title: Azure 互联网分析器故障排除 |微软文档
description: Azure Internet 分析器的故障排除参考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069211"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure 互联网分析器故障排除

本文包含常见 Internet 分析器问题的故障排除步骤。

## <a name="things-to-keep-in-mind"></a>要点
- 客户端脚本必须嵌入到**HTTPS**网站中。 如果脚本在纯文本 **（http://）** 或本地 **（file://**） 网站中运行，则不会收集度量值。
- 仅当 Internet Analyzer 配置文件的客户端脚本已嵌入到接收实际用户流量的应用程序中时，才会收集测量数据。 合成流量（例如，Azure WebApp 性能测试）通常不会执行嵌入式 Javascript 代码，因此该类型的流量不会生成度量。

## <a name="azure-portal"></a>Azure 门户
**记分卡部分中的"尚未为选定的筛选器组合生成记分卡"**
- 记分卡每天生成（在每天结束时，UTC 时间）。
- 仅当为所选筛选器组合（测试、时间段、国家/地区等）收集了 100 多个测量值时，才会生成记分卡。

**测试中一个或两个端点的"总测量计数"为零**
- 时间序列和测量计数每小时计算一次，因此您需要等待至少该时间才能显示新的测量数据。
- 互联网分析器仅计算其分析的成功测量（即 HTTP 200 响应）。 如果测试中的一个或两个终结点无法访问或返回非 200 HTTP 代码，则它们将显示为零总测量值。

## <a name="next-steps"></a>后续步骤
阅读[互联网分析器常见问题解答](internet-analyzer-faq.md)

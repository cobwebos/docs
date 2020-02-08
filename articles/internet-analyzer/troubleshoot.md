---
title: Azure Internet 分析器故障排除 |Microsoft Docs
description: Azure Internet 分析器的故障排除参考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069211"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet 分析器故障排除

本文包含常见 Internet 分析器问题的故障排除步骤。

## <a name="things-to-keep-in-mind"></a>要点
- 必须将客户端脚本嵌入到**HTTPS**网站中。 如果脚本在纯文本（**http://** ）或本地（**file://** ）网站中运行，则不会收集度量值。
- 仅当 Internet 分析器配置文件的客户端脚本已嵌入到接收真实用户流量的应用程序中时，才会收集度量数据。 综合流量（例如，Azure WebApp 性能测试）通常不执行嵌入的 Javascript 代码，因此不会通过该类型的流量生成度量。

## <a name="azure-portal"></a>Azure 门户
**"记分卡部分中未为所选筛选器组合生成记分卡"**
- 每日生成记分卡（在每天的结束时间，UTC 时间）。
- 仅当为所选筛选器组合（测试、时间段、国家/地区等）收集了100多个度量值时，才会生成记分卡。

**测试中的一个或两个终结点的 "总度量计数" 为零**
- 时序和度量值计数每小时计算一次，因此需要等待至少一段时间才能显示新的度量值数据。
- Internet 分析器仅对其分析的成功度量值（即 HTTP 200 响应）进行计数。 如果测试中的一个或两个终结点无法访问或返回非 200 HTTP 代码，则它们将显示为零总度量值。

## <a name="next-steps"></a>后续步骤
阅读 [Internet 分析器常见问题解答](internet-analyzer-faq.md)

---
title: Azure Internet 分析器故障排除 |Microsoft Docs
description: Azure Internet 分析器的故障排除参考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897385"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet 分析器故障排除

本文包含常见 Internet 分析器问题的故障排除步骤。

## <a name="azure-portal"></a>Azure 门户
**记分卡部分中的 "未为此记分卡收集足够的度量值"**

请注意：
- 仅当 Internet 分析器配置文件的客户端脚本已嵌入到接收真实用户流量的应用程序中时，才会收集度量数据。 综合流量（例如，Azure WebApp 性能测试）通常不执行嵌入的 Javascript 代码，因此不会通过该类型的流量生成度量。
- 时序每小时生成一次，因此需要等待至少一段时间才能显示新的度量数据。
- 每日生成记分卡（在每天的结束时间，UTC 时间）。
- 仅当为所选筛选器组合（测试、时间段、国家/地区等）收集了100多个度量值时，才会生成记分卡。

## <a name="next-steps"></a>后续步骤
阅读 [Internet 分析器常见问题解答](internet-analyzer-faq.md)

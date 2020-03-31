---
title: 将梭子鱼云根防火墙连接到 Azure 哨兵*微软文档
description: 了解如何将梭子鱼云根防火墙连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aaedbfdd3b1bbbc653756d74ee86fc277b21caec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588495"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>连接 Barracuda CloudGen 防火墙

梭子鱼云根防火墙 （CGFW） 连接器可让您轻松地将梭子鱼 CGFW 日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 这使您可以更深入地了解组织的网络，并改进您的安全操作功能。




## <a name="prerequisites"></a>先决条件

- 读取和写入 Azure Sentinel 工作区的权限。

- 梭子鱼云根防火墙必须配置为通过 Syslog 导出日志。

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>将 Azure 哨兵连接到梭子鱼云根防火墙

1. 在 Azure 门户中，导航到**Azure 哨兵** > **数据连接器**，然后选择**梭子鱼云根防火墙**连接器。

2. 选择 **"打开连接器"页**。

3. 按照**梭子鱼云根防火墙**页面上的说明进行操作。


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将梭子鱼云根防火墙连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



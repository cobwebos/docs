---
title: 连接脉冲将安全数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何连接脉冲将安全数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: f7f6de717f0ed9a2e27e15199b6f0ea566e0fdcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021624"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>将脉冲连接安全连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的脉冲连接安全数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何将 [脉冲连接安全](https://www.pulsesecure.net/products/pulse-connect-secure/) 设备连接到 Azure Sentinel。 通过脉冲连接安全数据连接器，可以轻松地将脉冲连接安全日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报和改进调查。 脉冲连接安全与 Azure Sentinel 之间的集成利用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>前进脉冲将安全日志连接到 Syslog 代理  

配置脉冲连接安全，通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，然后选择 " **脉冲连接安全** 连接器"。

1. 选择 " **打开连接器" 页面**。

1. 按照 " **脉冲连接安全** " 页上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 Syslog 下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将脉冲连接安全连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
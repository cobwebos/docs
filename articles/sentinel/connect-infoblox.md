---
title: 将 Infoblox 网络标识操作系统（NIOS）数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Infoblox 网络标识操作系统（NIOS）数据连接到 Azure Sentinel。
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531076"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>将 Infoblox NIOS 连接到 Azure Sentinel

本文介绍了如何将[Infoblox 网络标识操作系统（NIOS）设备](https://www.infoblox.com/glossary/network-identity-operating-system-nios/)连接到 Azure Sentinel。 Infoblox NIOS 数据连接器可让你轻松地将 Infoblox 日志与 Azure Sentinel 连接，查看仪表板，创建自定义警报，并改善调查。 Infoblox NIOS 与 Azure Sentinel 之间的集成利用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>将 Infoblox 日志转发到 Syslog 代理  

配置 Infoblox 以通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区。

1. 在 Azure Sentinel 门户中，单击 "**数据连接器**"，然后选择 " **Infoblox NIOS** connector"。

1. 选择 "**打开连接器" 页面**。

1. 按照**INFOBLOX NIOS**页上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 Syslog 下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Infoblox NIOS 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
---
title: 将福丁特数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Fortinet 数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588189"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>将福丁特连接到 Azure 哨兵



本文介绍如何将 Fortinet 设备连接到 Azure Sentinel。 Fortinet 数据连接器允许您轻松地将 Fortinet 日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 在 Azure Sentinel 上使用 Fortinet 将为您提供更多有关组织 Internet 使用情况的见解，并将增强其安全操作功能。 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>将福汀特日志转发到 Syslog 代理

将 Fortinet 配置为通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 打开 Fortinet 设备上的 CLI 并运行以下命令：

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - 将服务器**IP 地址**替换为代理的 IP 地址。
    - 将**syslog 端口**设置为**514**或代理上的端口集。
    - 要在早期 FortiOS 版本中启用 CEF 格式，您可能需要运行命令集**csv 禁用**。
 
   > [!NOTE] 
   > 有关详细信息，请访问[福廷特文档库](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)。 选择您的版本，并使用**手册**和**日志消息参考**。

1. 要在 Azure 监视器日志分析中为 Fortinet 事件使用相关架构`CommonSecurityLog`，请搜索 。

1. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。


## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何将 Fortinet 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



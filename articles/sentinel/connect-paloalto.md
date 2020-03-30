---
title: 将帕洛阿尔托网络数据连接到 Azure 哨兵*微软文档
description: 了解如何将帕洛阿尔托网络数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588121"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>将帕洛阿尔托网络连接到 Azure 哨兵



本文介绍如何将帕洛阿尔托网络设备连接到 Azure Sentinel。 帕洛阿尔托网络数据连接器允许您轻松地将帕洛阿尔托网络日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 在 Azure Sentinel 上使用 Palo Alto 网络将为您提供更多有关组织 Internet 使用情况的见解，并将增强其安全操作功能。 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>将帕洛阿尔托网络日志转发到 Syslog 代理

配置 Palo Alto 网络，通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区：
1.  转到[常见事件格式 （CEF） 配置指南](https://docs.paloaltonetworks.com/resources/cef)，并下载适用于设备类型的 pdf。 按照指南中的所有说明设置您的帕洛阿尔托网络设备来收集 CEF 事件。 

1.  转到[配置 Syslog 监视](https://aka.ms/asi-syslog-paloalto-forwarding)，然后按照步骤 2 和 3 配置 CEF 事件从帕洛阿尔托网络设备转发到 Azure Sentinel。

    1. 请确保将**Syslog 服务器格式**设置为**BSD**。

       > [!NOTE]
       > PDF 中的副本/粘贴操作可能会更改文本并插入随机字符。 为了避免这种情况，请将文本复制到编辑器，并在粘贴日志格式之前删除任何可能破坏日志格式的字符，如本示例所示。
 
        ![CEF 文本复制问题](./media/connect-cef/paloalto-text-prob1.png)

1. 要在 Log Alto 网络事件的日志分析中使用相关架构，请搜索**通用安全日志**。

1. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。




## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将帕洛阿尔托网络设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



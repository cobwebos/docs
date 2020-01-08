---
title: 将 Palo Alto Networks 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Palo Alto Networks 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: bfdf961906626b485f07ddd29da9b8509dc53cc0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610516"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>将 Palo Alto Networks 连接到 Azure Sentinel



本文介绍了如何将 Palo Alto 网络设备连接到 Azure Sentinel。 Palo Alto Networks 数据连接器可让你轻松连接 Palo Alto 网络日志和 Azure Sentinel，查看仪表板、创建自定义警报，以及改进调查。 使用 Azure 上的 Palo Alto 网络可以更深入地了解组织的 Internet 使用情况，并增强其安全操作功能。 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>将 Palo Alto Networks 日志转发到 Syslog 代理

将 Palo Alto Networks 配置为通过 Syslog 代理将 Syslog 消息以 CEF 格式转发到 Azure 工作区：
1.  请参阅[通用事件格式（CEF）配置指南](https://docs.paloaltonetworks.com/resources/cef)，并下载适用于你的设备类型的 pdf。 按照指南中的所有说明设置 Palo Alto Networks 设备，收集 CEF 事件。 

1.  转到 "[配置 Syslog 监视](https://aka.ms/asi-syslog-paloalto-forwarding)"，然后执行步骤2和步骤3，将 CEF 事件从 Palo Alto 网络设备转发到 Azure Sentinel。

    1. 请确保将**Syslog 服务器格式**设置为**BSD**。

       > [!NOTE]
       > PDF 中的复制/粘贴操作可能会更改文本并插入随机字符。 若要避免这种情况，请将文本复制到编辑器，并在粘贴之前删除可能会破坏日志格式的任何字符，如本示例中所示。
 
        ![CEF 文本复制问题](./media/connect-cef/paloalto-text-prob1.png)

1. 若要在 Palo Alto Networks 事件的 Log Analytics 中使用相关架构，请搜索**CommonSecurityLog**。

1. 继续执行[步骤3：验证连接性](connect-cef-verify.md)。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Palo Alto 网络设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



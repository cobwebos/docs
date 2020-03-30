---
title: 将 Windows 防火墙数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Windows 防火墙数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588053"
---
# <a name="connect-windows-firewall"></a>连接 Windows 防火墙



Windows 防火墙连接器允许您轻松连接 Windows 防火墙日志（如果它们已连接到 Azure Sentinel 工作区）。 此连接使您能够查看仪表板、创建自定义警报和改进调查。 这使您可以更深入地了解组织的网络，并改进您的安全操作功能。 该解决方案从安装日志分析代理的 Windows 计算机收集 Windows 防火墙事件。 


> [!NOTE]
> - 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。
> - 如果将 Azure Sentinel 和 Azure 安全中心收集到同一工作区，则无需通过此连接器启用 Windows 防火墙解决方案。 如果启用了它，它将不会导致重复的数据。 

## <a name="enable-the-connector"></a>启用连接器 

1. 在 Azure Sentinel 门户中，选择 **"数据"连接器**，然后单击**Windows 防火墙**磁贴。 
1.  如果 Windows 计算机位于 Azure 中：
    1. 单击**Azure Windows 虚拟机上的安装代理**。
    1. 在 **"虚拟机**"列表中，选择要流式传输到 Azure Sentinel 的 Windows 计算机。 请确保这是 Windows VM。
    1. 在为该 VM 打开的窗口中，单击"**连接**"。  
    1. 单击 Windows**防火墙连接器**窗口中**的启用**。 

2. 如果 Windows 计算机不是 Azure VM：
    1. 单击**非 Azure 计算机上安装代理**。
    1. 在 **"直接代理"** 窗口中，选择 **"下载 Windows 代理（64 位）"** 或 **"下载 Windows 代理"（32 位）。**
    1. 在 Windows 计算机上安装代理。 复制**工作区 ID、****主键**和**辅助键**，并在安装过程中提示时使用它们。

4. 选择要流式传输的数据类型。
5. 单击 **"安装解决方案**"。
6. 要在 Windows 防火墙的日志分析中使用相关架构，请搜索**安全事件**。

## <a name="validate-connectivity"></a>验证连接

可能需要 20 分钟以上，直到日志开始出现在日志分析中。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Windows 防火墙连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。


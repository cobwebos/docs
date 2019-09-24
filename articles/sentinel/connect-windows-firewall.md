---
title: 将 Windows 防火墙数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Windows 防火墙数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 840e8b3bc86281a8c42689b1cb68917741ef2bd9
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240628"
---
# <a name="connect-windows-firewall"></a>连接 Windows 防火墙



Windows 防火墙连接器可让你轻松连接到你的 Windows 防火墙日志（如果它们已连接到 Azure Sentinel 工作区）。 此连接使你可以查看仪表板、创建自定义警报和改进调查。 这样，你就可以更深入地了解组织的网络并改善安全操作功能。 解决方案从安装了 Log Analytics 代理的 Windows 计算机中收集 Windows 防火墙事件。 


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="enable-the-connector"></a>启用连接器 

1. 在 Azure Sentinel 门户中，选择 "**数据连接器**"，然后单击 " **Windows 防火墙**" 磁贴。 
1.  如果 Windows 计算机位于 Azure 中：
    1. 单击 "**在 Azure Windows 虚拟机上安装代理"** 。
    1. 在 "**虚拟机**" 列表中，选择要流式传输到 Azure Sentinel 的 Windows 计算机。 请确保这是一个 Windows VM。
    1. 在为该 VM 打开的窗口中，单击 "**连接**"。  
    1. 单击 " **Windows 防火墙连接器**" 窗口中的 "**启用**"。 

2. 如果 Windows 计算机不是 Azure VM：
    1. 单击 "**在非 Azure 计算机上安装代理"** 。
    1. 在 "**直接代理**" 窗口中，选择 "**下载 windows 代理（64位）** " 或 "**下载 windows 代理（32位）** "。
    1. 在 Windows 计算机上安装代理。 复制**工作区 ID**、**主密钥**和**辅助密钥**，并在安装过程中出现提示时使用它们。

4. 选择要流式传输的数据类型。
5. 单击 "**安装解决方案**"。
6. 若要在 Windows 防火墙 Log Analytics 中使用相关架构，请搜索**SecurityEvent**。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Windows 防火墙连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。


---
title: 将 Windows Defender 防火墙数据连接到 Azure Sentinel |Microsoft Docs
description: 启用 Azure Sentinel 中的 Windows 防火墙连接器，以便从安装了 Log Analytics 代理的 Windows 计算机轻松地流式传输防火墙事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: b2cf984e629d6b86beef9292dac819b554f49749
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850689"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>将具有高级安全性的 Windows Defender 防火墙连接到 Azure Sentinel

[具有高级安全性的 Windows Defender 防火墙](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security)连接器允许 Azure Sentinel 从工作区中的任何 Windows 计算机轻松引入具有高级安全日志的 Windows defender 防火墙。 通过此连接，你可以查看和分析工作簿中的 Windows 防火墙事件，并在创建自定义警报时使用它们，并将它们合并到安全调查中，使你能够更深入地了解组织的网络并改善安全操作功能。 

解决方案从安装了 Log Analytics 代理的 Windows 计算机中收集 Windows 防火墙事件。 

> [!NOTE]
> - 数据将存储在运行 Azure Sentinel 的工作区的地理位置。
>
> - 如果将 Azure Sentinel 和 Azure 安全中心收集到相同的工作区，则无需通过此连接器启用 Windows 防火墙解决方案。 如果仍启用此功能，则不会导致重复的数据。 

## <a name="prerequisites"></a>先决条件

- 您必须具有对要监视的计算机连接到的工作区的读取和写入权限。

- 除了任何**Azure Sentinel**角色外，还必须在该工作区上的 SecurityInsights 解决方案中分配**Log Analytics 参与者**角色。 [了解详细信息](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>启用连接器 

1. 在 Azure Sentinel 门户的导航菜单中，选择 "**数据连接器**"。

1. 从 "连接器库" 中选择 " **Windows 防火墙**"，然后单击 "**打开连接器" 页面**。

### <a name="instructions-tab"></a>说明选项卡

- **如果 Windows 计算机位于 Azure 中：**

    1. 选择 **"在 Azure Windows 虚拟机上安装代理"**。

    1. 单击 "**下载 & 适用于 Azure Windows 虚拟机的代理 >** " 链接 "。

    1. 在 "**虚拟机**" 列表中，选择要流式传输到 Azure Sentinel 的 Windows 计算机。  (可以在 OS 列筛选器中选择**windows** ，以确保仅) 显示 windows vm。

    1. 在为该 VM 打开的窗口中，单击 "**连接**"。

    1. 返回到 "**虚拟机**" 窗格，并为要连接的任何其他 vm 重复上述两个步骤。 完成后，返回到 " **Windows 防火墙**" 窗格。

- **如果 Windows 计算机不是 Azure VM：**

    1. 选择 **"在非 Azure Windows 计算机上安装代理"**。

    1. 单击 "**下载 & 非 Azure Windows 计算机的安装代理" >** 出现的链接。

    1. 在 "**代理管理**" 窗格中，根据需要选择 "**下载 windows 代理 (64 位) **或**下载 windows 代理 (32") **。

    1. 将**工作区 ID**、**主键**和**辅助密钥**字符串复制到文本文件。 将该文件和下载的安装文件复制到 Windows 计算机。 运行安装文件，并在出现提示时，在安装过程中输入文本文件中的 ID 和密钥字符串。

    1. 返回到 " **Windows 防火墙**" 窗格。

1. 单击 "**安装解决方案**"。

### <a name="next-steps-tab"></a>后续步骤选项卡

- 查看与**Windows 防火墙**数据连接器捆绑的可用建议工作簿和查询示例，以了解 windows 防火墙日志数据。

- 若要在**日志**中查询 Windows 防火墙数据，请在 "查询" 窗口中键入**windows 防火墙**。

## <a name="validate-connectivity"></a>验证连接
 
由于 Windows 防火墙日志仅在本地日志文件达到容量时才会发送到 Azure Sentinel，因此，将日志的默认大小保留为 4096 KB 最有可能导致收集延迟较高。 可以通过降低日志文件的大小来降低延迟时间。 请参阅[配置 Windows 防火墙日志](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)的说明。 请注意，在定义可能的最小日志大小 (1 KB) 会完全消除回收延迟，这可能会对本地计算机的性能产生负面影响。 

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Windows 防火墙连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。


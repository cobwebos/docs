---
title: "使用 Azure Cloud Shell 窗口 | Microsoft Docs"
description: "概述如何使用 Azure Cloud Shell 窗口。"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 4eb5680c618d78e0722e1eb4a0f551f26b4dc902
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="using-the-azure-cloud-shell-window"></a>使用 Azure Cloud Shell 窗口

本文档介绍如何使用 Cloud Shell 窗口。

## <a name="swap-between-bash-and-powershell-environments"></a>在 Bash 和 PowerShell 环境之间交换
![](media/using-the-shell-window/env-selector.png)

使用 Cloud Shell 工具栏中的环境选择器可在 Bash 和 PowerShell 环境之间交换。

## <a name="restart-cloud-shell"></a>重新启动 Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> 重新启动 Cloud Shell 将重置计算机状态，Azure 文件共享未保存的所有文件都将丢失。

* 单击 Cloud Shell 工具栏中的重启图标可重置计算机状态。

## <a name="minimize--maximize-cloud-shell-window"></a>最小化和最大化 Cloud Shell 窗口
![](media/using-the-shell-window/minmax.png)
* 单击窗口右上角的最小化图标隐藏窗口。 再次单击 Cloud Shell 图标取消隐藏。
* 单击最大化图标将窗口设置为最大高度。 要将窗口还原到先前大小，请单击还原图标。

## <a name="concurrent-sessions"></a>并发会话
Cloud Shell 允许每个会话作为单独的 Bash 进程，因此可在多个浏览器选项卡之间实现多个并发会话。
如要退出会话，请务必从每个会话窗口退出，因为虽然每个进程都在同一计算机上运行，但他们彼此独立运行。

## <a name="copy-and-paste"></a>复制和粘贴
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>调整 Cloud Shell 窗口的大小
* 单击或向上/向下拖动工具栏边缘可调整 Cloud Shell 窗口的大小。

## <a name="scrolling-text-display"></a>滚动文本显示
* 使用鼠标或触摸板进行滚动以移动终端文本。

## <a name="changing-the-text-size"></a>更改文本大小
![](media/using-the-shell-window/text-size.png)
* 单击窗口左上角的设置图标，将鼠标悬停在“文本大小”选项上，选择所需的文本大小。 将在会话之间保留你的选择。

## <a name="exit-command"></a>退出命令
运行 `exit` 终止活动会话。 超过 20 分钟无交互操作将默认触发此行为。

## <a name="next-steps"></a>后续步骤

[Cloud Shell 中的 Bash 快速入门](quickstart.md)
[Cloud Shell 中的 PowerShell 快速入门](quickstart-powershell.md)
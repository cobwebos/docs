---
title: "使用 Azure Cloud Shell（预览版）窗口 | Microsoft Docs"
description: "Azure Cloud Shell 窗口演练。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 69316d271fc4847565d183293013580b61695731
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>使用 Azure Cloud Shell 窗口

本文档介绍如何使用 Azure Cloud Shell 窗口。

## <a name="concurrent-sessions"></a>并发会话
Cloud Shell 允许每个会话作为单独的 Bash 进程，因此可在多个浏览器选项卡之间实现多个并发会话。
如要退出会话，请务必从每个会话窗口退出，因为虽然每个进程都在同一计算机上运行，但他们彼此独立运行。

## <a name="restart-cloud-shell"></a>重新启动 Cloud Shell
![](media/recycle.png)
* 单击工具栏上的重新启动图标重置 Cloud Shell 计算机。

> ![警告]重新启动 Cloud Shell 将重置计算机状态，clouddrive 未保存的所有文件都将丢失。

## <a name="minimize--maximize-cloud-shell-window"></a>最小化和最大化 Cloud Shell 窗口
![](media/minmax.png)
* 单击窗口右上角的最小化图标隐藏窗口。 再次单击 Cloud Shell 图标取消隐藏。
* 单击最大化图标将窗口设置为最大高度。 若要将窗口还原到先前大小，请单击还原图标。

## <a name="copy-and-paste"></a>复制和粘贴
* Windows：请使用 `Ctrl-insert` 和 `Shift-insert`，或右键单击下拉列表进行复制/粘贴
  * FireFox/IE 可能不支持剪贴板权限
* Mac OS：请使用 `Cmd-c` 和 `Cmd-v`，或右键单击下拉列表进行复制/粘贴

## <a name="resize-cloud-shell-window"></a>调整 Cloud Shell 窗口的大小
* 单击或向上/向下拖动工具栏边缘可调整 Cloud Shell 窗口的大小。

## <a name="scrolling-text-display"></a>滚动文本显示
* 使用鼠标或触摸板进行滚动

## <a name="exit-command"></a>退出命令
运行 `exit` 终止活动会话。 超过 10 分钟无交互操将默认触发此行为。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md)  

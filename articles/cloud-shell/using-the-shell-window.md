---
title: 使用 Azure Cloud Shell 窗口 | Microsoft Docs
description: 概述如何使用 Azure Cloud Shell 窗口。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860325"
---
# <a name="using-the-azure-cloud-shell-window"></a>使用 Azure Cloud Shell 窗口

本文档介绍如何使用 Cloud Shell 窗口。

## <a name="swap-between-bash-and-powershell-environments"></a>在 Bash 和 PowerShell 环境之间交换

使用 Cloud Shell 工具栏中的环境选择器可在 Bash 和 PowerShell 环境之间交换。  
![选择环境](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>重启 Cloud Shell
单击 Cloud Shell 工具栏中的重启图标可重置计算机状态。  
![重启 Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> 重新启动 Cloud Shell 将重置计算机状态，Azure 文件共享未保存的所有文件都将丢失。

## <a name="change-the-text-size"></a>更改文本大小
单击窗口左上角的设置图标，将鼠标悬停在“文本大小”选项上，选择所需的文本大小。 将在会话之间保留你的选择。
![文本大小](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>更改字体
单击窗口左上角的 "设置" 图标，并将鼠标悬停在 "字体" 选项上，并选择所需的字体。  将在会话之间保留你的选择。
![Font](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>上传和下载文件
单击窗口左上角的 "上传/下载文件" 图标，然后选择 "上传" 或 "下载"。  
![上传/下载文件](media/using-the-shell-window/uploaddownload.png)
* 若要上载文件，请使用弹出窗口浏览到本地计算机上的文件，选择所需的文件，然后单击 "打开" 按钮。  文件将上传到`/home/user`目录。
* 对于 "下载文件"，请在弹出窗口中输入完全限定的文件路径（即，在默认情况下显示`/home/user`在目录下的路径），然后选择 "下载" 按钮。  
> [!NOTE] 
> 文件和文件路径在 Cloud Shell 中区分大小写。 仔细检查文件路径中的大小写。

## <a name="open-another-cloud-shell-window"></a>打开另一个 Cloud Shell 窗口
通过允许每个会话作为单独的进程存在，Cloud Shell 跨浏览器选项卡启用多个并发会话。
如要退出会话，请务必从每个会话窗口退出，因为虽然每个进程都在同一计算机上运行，但他们彼此独立运行。  
单击窗口左上角的 "打开新会话" 图标。 新选项卡将打开，并将另一个会话连接到现有容器。
![打开新会话](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell 编辑器
* 请参阅[使用 Azure Cloud Shell 编辑器](using-cloud-shell-editor.md)页。

## <a name="web-preview"></a>Web 预览
单击窗口左上角的 "web 预览" 图标，选择 "配置"，指定要打开的所需端口。  选择 "打开端口" 以仅打开端口，或单击 "打开并浏览" 打开端口并在新选项卡中预览端口。  
![Web 预览](media/using-the-shell-window/preview.png)  
<br>
![配置端口](media/using-the-shell-window/preview-configure.png)  
单击窗口左上角的 "web 预览" 图标，选择 "预览端口 ..."在新选项卡中预览打开的端口。单击窗口左上角的 "web 预览" 图标，选择 "关闭端口 ..."关闭打开的端口。  
![预览/关闭端口](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>最小化和最大化 Cloud Shell 窗口
单击窗口右上角的最小化图标隐藏窗口。 再次单击 Cloud Shell 图标取消隐藏。
单击最大化图标将窗口设置为最大高度。 要将窗口还原到先前大小，请单击还原图标。  
![最小化或最大化窗口](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>复制和粘贴
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>调整 Cloud Shell 窗口的大小
单击或向上/向下拖动工具栏边缘可调整 Cloud Shell 窗口的大小。

## <a name="scrolling-text-display"></a>滚动文本显示
使用鼠标或触摸板进行滚动以移动终端文本。

## <a name="exit-command"></a>退出命令
运行 `exit` 终止活动会话。 超过 20 分钟无交互操作将默认触发此行为。

## <a name="next-steps"></a>后续步骤

[Cloud Shell 中的 Bash 快速入门](quickstart.md) <br>
[Cloud Shell 中的 PowerShell 快速入门](quickstart-powershell.md)

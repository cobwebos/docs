---
title: "Azure Cloud Shell（预览版）限制 | Microsoft Docs"
description: "Azure Cloud Shell 的限制概述"
services: 
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
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3ac234b27a0675f484018c357a65ab65049ceee0
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 的限制
Azure Cloud Shell 有以下已知限制。

## <a name="system-state-and-persistence"></a>系统状态和持久性
提供 Cloud Shell 会话的计算机是暂时性的，在会话处于非活动状态 20 分钟后会被回收。 Cloud Shell 需要装载文件共享。 因此，订阅必须能够设置存储资源才能访问 Cloud Shell。 其他注意事项包括：
* 若有装载的存储，则仅保留 `$Home` 目录或 `clouddrive` 目录内的修改。
* 仅可从[已分配区域](persisting-shell-storage.md#mount-a-new-clouddrive)内部装载文件共享。
* Azure 文件仅支持本地冗余存储和异地冗余存储帐户。

## <a name="user-permissions"></a>用户权限
权限设置为普通用户，不具有 sudo 访问权限。 不会保留 `$Home` 目录外部的任何安装。
尽管 `clouddrive` 目录内的某些命令（如 `git clone`）不具有适当权限，但 `$Home` 目录具有相应权限。

## <a name="browser-support"></a>浏览器支持
Cloud Shell 支持最新版本的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 和 Apple Safari。 不支持专用模式下的 Safari。

## <a name="copy-and-paste"></a>复制和粘贴
因为在 Windows 计算机上，“Ctrl+C”和“Ctrl+V”不能作为键盘快捷方式执行复制和粘贴操作，而是使用“Ctrl+Insert”和“Shift+Insert”执行复制和粘贴操作。

还可以右键单击，选择复制/粘贴选项，但该右键单击功能取决于特定于浏览器的剪贴板访问权限。

## <a name="editing-bashrc"></a>编辑 .bashrc
编辑 .bashrc 时请格外小心，因为执行这一操作可能导致 Cloud Shell 中出现意外错误。

## <a name="bashhistory"></a>.bash_history
由于 Cloud Shell 会话中断或并发会话，bash 命令的历史记录可能不一致。

## <a name="usage-limits"></a>使用限制
Cloud Shell 适用于交互式用例。 因此，任何长时间运行的非交互式会话都会在没有预警的情况下终止。

## <a name="network-connectivity"></a>网络连接
Cloud Shell 中的任何延迟均取决于本地 Internet 连接，且 Cloud Shell 会在发送指示后继续尝试操作。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md)


---
title: "Azure Cloud Shell（预览版）限制 | Microsoft Docs"
description: "Azure Cloud Shell 的限制概述"
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
ms.openlocfilehash: c007b73375c8c82248228f4e549c0ac95640d7ec
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017

---

# <a name="limitations-for-azure-cloud-shell"></a>Azure Cloud Shell 的限制
Azure Cloud Shell 有以下已知限制。

## <a name="system-state-and-persistence"></a>系统状态和持久性
提供 Cloud Shell 会话的计算机是暂时性的，在会话处于非活动状态 10 分钟后将被回收。 Cloud Shell 需要装载文件共享。
* 若有装载的存储，则仅保留 `$Home` 目录或 `clouddrive` 目录内的修改
  * 仅可从[已分配区域](persisting-shell-storage.md#pre-requisites-for-manual-mounting)内部装载文件共享
  * Azure 文件仅支持 LRS 和 GRS 存储帐户

## <a name="user-permissions"></a>用户权限
权限设置为普通用户，不具有 sudo 访问权限。 不会保留 $Home 外部的任何安装。
某些命令（如 `clouddrive` 目录内的 `git clone`）不具有适当权限，但 $Home 目录具有相应权限。

## <a name="browser-support"></a>浏览器支持
Cloud Shell 支持最新版本的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 和 Apple Safari。 不支持专用模式下的 Safari。

## <a name="copy-and-paste"></a>复制和粘贴
Ctrl-v 和 Ctrl-c 不会像在 Windows 计算机上起到复制/粘贴的作用，请使用 Ctrl-insert 和 Shift-insert 进行复制/粘贴。
还可以右键单击，选择复制/粘贴选项，但这取决于特定于浏览器的剪贴板访问权限。

## <a name="usage-limits"></a>使用限制
Cloud Shell 主要用于交互式用例，因此，任何长时间运行的非交互式会话都将在没有预警的情况下终止。

## <a name="network-connectivity"></a>网络连接
Cloud Shell 中的任何延迟均取决于本地 Internet 连接，Cloud Shell 将在发送指示后继续尝试操作。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md)

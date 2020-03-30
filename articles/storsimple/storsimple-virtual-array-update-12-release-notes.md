---
title: 微软 Azure StorSimple 虚拟阵列更新 1.2 发行说明*微软文档
description: 描述运行更新 1.2 的 StorSimple 虚拟阵列的关键打开问题和解决方法。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420598"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 虚拟阵列更新 1.2 发行说明

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会不断更新。 一旦发现需要解决的关键问题，将会添加到此处。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

更新 1.2 对应于软件版本**10.0.10311.0**。

> [!IMPORTANT]
> - 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。 有关用于应用此更新的软件包的详细说明，请转到[下载更新 1.2](#download-update-12)。
> - 仅当设备正在运行更新 1.0 或 1.1 时，更新 1.2 才可通过 Azure 门户获得。

## <a name="whats-new-in-update-12"></a>Update 1.2 中的新增功能

此更新包含以下错误修复：

- 在处理已删除的文件时提高了弹性。
- 改进了代码启动路径中的处理异常，减少了备份、还原、云读取和自动空间回收中的故障。

## <a name="download-update-12"></a>下载更新 1.2

要下载此更新，请转到[Microsoft 更新目录](https://www.catalog.update.microsoft.com/Home.aspx)服务器，然后下载 KB4502035 包。 此包包含以下包：

 - **KB4493446，** 其中包含截至 2019 年 4 月的 2012 R2 的累积 Windows 更新。 有关此汇总中包含的内容的详细信息，请访问[4 月每月安全汇总](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - **KB3011067，** 这是微软更新独立软件包文件 WindowsTH-KB3011067-x64。 此文件用于更新设备软件。

下载KB4502035，并按照这些说明[通过本地 Web UI 应用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 不是。 | Feature | 问题 |
| --- | --- | --- |
| 1 |删除| 在该软件的早期版本中，即使删除文件，设备的使用也没有更改，因此出现问题。 此问题已在此版本中修复。 在处理已删除的文件时，分层代码路径更具弹性。|
| 2 |异常处理| 在软件的早期版本中，系统重新启动后出现问题，可能导致备份、还原、从云读取和自动空间回收失败。 此版本包含有关在启动路径中处理异常的方式的更改。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知问题

更新 1.2 中未注意到任何新问题。 所有发布说明的问题都从以前的版本中结转。 要查看以前版本中包含的已知问题的摘要，请转到[Update 1.1 中的已知问题](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>后续步骤

下载KB4502035，[并通过本地 Web UI 应用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>参考

查找较旧版本的发行说明？ 请转到：
* [StorSimple 虚拟阵列更新 1.1 发行说明](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 虚拟阵列更新 1.0 发行说明](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 虚拟阵列 Update 0.6 发行说明](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple 虚拟阵列更新 0.5 发行说明](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 虚拟阵列更新 0.4 发行说明](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 虚拟阵列更新 0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](storsimple-ova-pp-release-notes.md)

---
title: Microsoft Azure StorSimple 虚拟阵列 Update 1.2 发行说明 |Microsoft Docs
description: 介绍 StorSimple Virtual Array 运行 Update 1.2 的重大待解决问题和解决方法。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420598"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 虚拟阵列 Update 1.2 发行说明

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会不断更新。 一旦发现需要解决的关键问题的它们将添加。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

Update 1.2 对应于软件版本**10.0.10311.0**。

> [!IMPORTANT]
> - 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。 有关用于将应用此更新的包的详细说明，请转到[下载 Update 1.2](#download-update-12)。
> - Update 1.2 可供你通过 Azure 门户仅当设备运行的 Update 1.0 或 1.1。

## <a name="whats-new-in-update-12"></a>Update 1.2 中的新增功能

此更新包含以下 bug 修复：

- 改进了复原能力处理时删除文件。
- 改进了的处理异常导致的代码启动路径中减少备份、 还原、 云读取失败，并且自动空间回收。

## <a name="download-update-12"></a>下载 Update 1.2

若要下载此更新，请转到[Microsoft Update 目录](https://www.catalog.update.microsoft.com/Home.aspx)服务器和下载 KB4502035 包。 此包包含以下包：

 - **KB4493446** ，2019 年 4 月到 2012 R2 包含 Windows 的累积更新。 有关此汇总中包含的内容的详细信息，请转到[年 4 月月度安全性汇总](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - **KB3011067** WindowsTH KB3011067 x64 的 Microsoft 更新独立程序包文件。 此文件用于更新设备软件。

下载 KB4502035，并按照这些说明[应用通过本地 web UI 更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 不。 | Feature | 问题 |
| --- | --- | --- |
| 第 |删除| 在早期版本的软件，出现问题时出现未更改设备的使用情况，即使文件已删除。 在此版本中修复此问题。 处理已删除的文件时，已更具弹性分层代码路径。|
| 2 |异常处理| 在早期版本的软件，时，可能会导致备份、 还原、 从云中读取中的失败和自动空间回收在系统重新启动后出现问题。 此版本包含有关异常的方式处理启动路径的更改。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知问题

任何新的问题不已在 Update 1.2 发行说明。 从以前的版本中的所有发行说明的问题会都累加。 若要查看先前版本中包含的已知问题的摘要，请转到[Update 1.1 中的已知问题](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>后续步骤

下载 KB4502035 并[应用通过本地 web UI 更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>参考

查找较旧版本的发行说明？ 请转到：
* [StorSimple 虚拟阵列 Update 1.1 发行说明](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 虚拟阵列 Update 1.0 发行说明](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 虚拟阵列 Update 0.6 发行说明](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array 更新 0.5 发行说明](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 虚拟阵列更新 0.4 发行说明](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](storsimple-ova-pp-release-notes.md)

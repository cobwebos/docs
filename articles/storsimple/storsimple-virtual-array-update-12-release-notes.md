---
title: Microsoft Azure StorSimple 虚拟阵列更新1.2 发行说明 |Microsoft Docs
description: 描述运行更新1.2 的 StorSimple 虚拟阵列的重要打开问题和解决方法。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "66420598"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 虚拟阵列更新1.2 发行说明

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续更新。 一旦发现需要解决的关键问题，将会添加到此处。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

更新1.2 对应于软件版本 **10.0.10311.0**。

> [!IMPORTANT]
> - 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。 有关用于应用此更新的包的详细说明，请参阅 [下载更新 1.2](#download-update-12)。
> - 仅当设备运行的是 Update 1.0 或1.1 时，才能通过 Azure 门户获取更新1.2。

## <a name="whats-new-in-update-12"></a>Update 1.2 中的新增功能

此更新包含下列 bug 修复：

- 提高了在处理已删除文件时的复原能力。
- 改进了代码启动路径中的处理异常，从而减少了备份、还原、云读取和自动空间回收的故障。

## <a name="download-update-12"></a>下载更新1。2

若要下载此更新，请前往 [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Home.aspx) 服务器，并下载 KB4502035 包。 此包包含以下包：

 - **KB4493446** ，其中包含 2012 R2 的累积 Windows 更新，最高可达4月2019。 有关此汇总中包含的内容的详细信息，请参阅 [四月每月安全](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)更新。
 - **KB3011067** 是 WINDOWSTH-KB3011067-X64 Microsoft 更新独立包文件。 此文件用于更新设备软件。

下载 KB4502035，并按照这些说明 [通过本地 WEB UI 应用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 不是。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |删除| 在软件的以前版本中，即使在删除文件时设备的使用未更改，也会出现问题。 此问题已在此版本中解决。 在处理已删除的文件时，分层代码路径更具弹性。|
| 2 |异常处理| 在软件的以前版本中，系统重新启动后会出现问题，这可能会导致备份、还原、从云读取以及自动回收空间时出现故障。 此版本包含对在启动路径中处理异常的方式所做的更改。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知问题

没有发布新问题-请在更新1.2 中注明。 所有版本记录的问题都是从以前的版本中进行的。 若要查看以前版本中包含的已知问题的摘要，请访问 [更新1.1 中的已知问题](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>后续步骤

下载 KB4502035 并 [通过本地 WEB UI 应用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>参考

查找较旧版本的发行说明？ 请转到：
* [StorSimple 虚拟阵列更新1.1 发行说明](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 虚拟阵列更新1.0 发行说明](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 虚拟阵列 Update 0.6 发行说明](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple 虚拟阵列更新0.5 发行说明](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 虚拟阵列更新0.4 发行说明](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 虚拟阵列更新0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](storsimple-ova-pp-release-notes.md)

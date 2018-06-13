---
title: StorSimple 8000 系列 Update 1.2 发行说明 | Microsoft Docs
description: 介绍 StorSimple 8000 系列 Update 1.2 的新功能、问题和解决方法。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67603c04cf7b5f45517d16620e5b79241aaae94d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057955"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>适用于 StorSimple 8000 系列设备的 Update 1.2 发行说明

## <a name="overview"></a>概述
以下发行说明描述 StorSimple 8000 系列 Update 1.2 的新功能，并标识其重要的待解决问题。 其中还包含此版本中随附的 StorSimple 软件、驱动程序和磁盘固件更新的列表。 

Update 1.2 可应用于任何运行 Release (GA)、Update 0.1、Update 0.2 或 Update 0.3 软件的 StorSimple 设备。 如果设备运行的是 Update 1 或 Update 1.1，则无法使用 Update 1.2。 如果设备运行的是发行版 (GA)，请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)以获取有关安装此更新的帮助。

下表列出与 Update 1、1.1 和 1.2 的对应设备软件版本。

| 如果正在运行更新... | 这是设备软件版本。 |
| --- | --- |
| Update 1.2 |6.3.9600.17584 |
| Update 1.1 |6.3.9600.17521 |
| Update 1.0 |6.3.9600.17491 |

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。 有关详细信息，请参阅如何[在 StorSimple 设备上安装 Update 1.2](storsimple-install-update-1.md)。 

> [!IMPORTANT]
> * 安装此更新大约需要 5-10 小时（包括 Windows Update）。 
> * Update 1.2 具有软件、LSI 驱动程序和磁盘固件更新。 若要安装，请按照[在 StorSimple 设备上安装 Update 1.2](storsimple-install-update-1.md) 中的说明执行操作。
> * 对于新版本，由于我们分阶段推出更新，可能不能立即看到更新。 请在数天内再次扫描更新，因为很快就会提供这些更新。
> 
> 

## <a name="whats-new-in-update-12"></a>Update 1.2 中的新增功能
这些功能在 Update 1 中首次发布，仅供有限用户使用。 在 Update 1.2 版本中，大多数的 StorSimple 用户都会看到以下新功能和改进：

* **从 5000-7000 系列迁移到 8000 系列设备** — 此版本引入了一个新的迁移功能，可让 StorSimple 5000-7000 系列设备用户将他们的数据迁移到 StorSimple 8000 系列物理设备或虚拟设备。 迁移功能具有以下两个重要的价值主张：                                                                  
  
  * **业务连续性**，通过将 5000-7000 系列设备上的现有数据迁移到 8000 系列设备来实现。
  * **8000 系列设备的改进功能**，例如通过 StorSimple Manager 服务对多个设备进行高效的集中式管理、更高级的硬件和已更新的固件、虚拟设备、数据移动性，以及未来路线图中的功能。
    
    有关如何将 StorSimple 5000-7000 系列迁移到 8000 系列设备的详细信息，请参阅[迁移指南](http://www.microsoft.com/download/details.aspx?id=47322)。 
* **Azure Government 门户中的可用性** — Azure Government 门户中现已提供 StorSimple。 请参阅如何[在 Azure Government 门户中部署 StorSimple 设备](storsimple-deployment-walkthrough-gov.md)。
* **支持其他云服务提供商** — 支持的其他云服务提供商有 Amazon S3、具有 RRS 的 Amazon S3、HP 和 OpenStack（试用版）。
* **最新存储 API 的更新** — 通过此版本，StorSimple 已更新到最新的 Azure 存储服务 API。 运行 Update 1 前的软件版本（版本 0.1、0.2 和 0.3）的 StorSimple 8000 系列设备，使用的是 2009 年 7 月 17 日之前的 Azure 存储服务 API 版本。 如更新的[有关存储服务版本删除的公告](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)中所述，这些 API 将于 2016 年 8 月 1 日被弃用。 请务必在 2016 年 8 月 1 日之前应用 StorSimple 8000 系列 Update 1。 如果无法执行此操作，StorSimple 设备将停止正常工作。
* **区域冗余存储空间 (ZRS) 的支持** — 通过升级到最新版本的存储 API，除了本地冗余存储 (LRS) 和异地冗余存储 (GRS)，StorSimple 8000 系列还将支持区域冗余存储空间 (ZRS)。 有关 ZRS 的详细信息，请参阅这篇[有关 Azure 存储冗余选项](../storage/common/storage-redundancy.md)的文章。
* **增强的初始部署和更新体验** — 在此版本中，安装和更新过程已得到增强。 通过安装向导的安装过程已得到了改进，如果网络配置和防火墙设置不正确，可以为用户提供反馈。 提供了更多诊断 cmdlet，以帮助你对设备的网络进行故障排除。 有关用于故障排除的新诊断 cmdlet 的详细信息，请参阅这篇[部署故障排除](storsimple-troubleshoot-deployment.md)文章。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修复的问题
下表提供在 Update 1.2、1.1 和 1 中已修复问题的摘要。    

| 不会。 | 功能 | 问题 | 更新中已修复 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell for StorSimple |用户远程使用 Windows PowerShell for StorSimple 访问 StorSimple 设备，再启动安装向导时，只要输入 Data 0 IP 就会发生崩溃。 这个 bug 现已在 Update 1 中修复。 |Update 1 |是 |是 |
| 2 |恢复出厂设置 |在某些情况下，执行恢复出厂设置时，StorSimple 设备会卡住并显示此消息：**正在进行恢复出厂设置 (阶段 8)**。 如果在该 cmdlet 正在运行时按下 CTRL+C，就会发生这种情况。 这个 bug 现已修复。 |Update 1 |是 |否 |
| 3 |恢复出厂设置 |双控制器恢复出厂设置失败后，允许继续进行设备注册。 这会导致不支持的系统配置。 在 Update 1 中，会显示一条错误消息并阻止在恢复出厂设置失败的设备上注册。 |Update 1 |是 |否 |
| 4 |恢复出厂设置 |在某些情况下，会引发不匹配的误报。 在运行 Update 1 的设备上，不会再生成错误的不匹配警报。 |Update 1 |是 |否 |
| 5 |恢复出厂设置 |如果恢复出厂设置在完成之前被中断，设备会进入恢复模式，并且不允许访问 Windows PowerShell for StorSimple。 这个 bug 现已修复。 |Update 1 |是 |否 |
| 6 |灾难恢复 |灾难恢复 (DR) bug 已修复，其中 DR 在目标设备上的备份发现期间失败。 |Update 1 |是 |是 |
| 7 |监视 LED |在某些情况下，装置背面的监视 LED 没有指示正确的状态。 蓝色 LED 已关闭。 即使未配置这些接口时，DATA 0 和 DATA 1 的 LED 也在闪烁。 此问题已解决且监视 LED 现在指示正确的状态。 |Update 1 |是 |否 |
| 8 |监视 LED |在某些情况下，应用 Update 1 之后，主动控制器上的蓝色指示灯会关闭，导致难以识别主动控制器。 此修补程序版本中已解决这个问题。 |Update 1.2 |是 |否 |
| 9 |网络接口 |在以前的版本中，使用非可路由网关配置的 StorSimple 设备可能会脱机。 在此版本中，已将 Data 0 的路由跃点数设为最低；因此，即使其他网络接口已启用云，来自该设备的所有云流量还是都通过 Data 0 路由。 |Update 1 |是 |是 |
| 10 |备份 |修补程序版本 Update 1.1 中已修复 Update 1 中导致备份在 24 天后失败的 bug。 |Update 1.1 |是 |是 |
| 11 |备份 |早期版本中的一个 bug 致使云快照的性能不佳、更改率较低。 此修补程序版本中已解决这个 bug。 |Update 1.2 |是 |是 |
| 12 |更新 |Update 1 中的一个 bug 报告升级失败，导致控制器进入恢复模式。此修补程序版本中已解决这个 bug。 |Update 1.2 |是 |是 |

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知问题
下表提供了此版本中已知问题的摘要。

| 不会。 | 功能 | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |磁盘仲裁 |在极少数情况下，如果 8600 设备的 EBOD 机箱中的大部分磁盘断开连接，导致没有磁盘仲裁，然后存储池会脱机。 即使磁盘重新连接，存储池也将保持脱机状态。 |需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |是 |否 |
| 2 |控制器 ID 错误 |更换控制器后，控制器 0 可能显示为控制器 1。 在更换控制器的过程中，从对等节点加载映像时，控制器 ID 刚开始可能显示为对等控制器的 ID。 在极少数情况下，此行为也可能在系统重新启动后出现。 |不需要用户操作。 控制器更换过程完成后，这种情况会自动解决。 |是 |否 |
| 3 |存储帐户 |此版本不支持使用存储服务删除存储帐户， 否则会导致无法检索用户数据。 |是 |是 | |
| 4 |设备故障转移 |不支持从同一源设备将某个卷容器多次故障转移到不同的目标设备。 当设备故障转移是从单个不活动的设备故障转移到多个设备时，会使第一个故障转移设备上卷容器丢失数据所有权。 进行此类故障转移后，在 Azure 经典门户中查看这些卷容器时，会发现它们的显示或表现有所不同。 | |是 |否 |
| 5 |安装 |安装 StorSimple Adapter for SharePoint 期间，需要提供设备 IP 才能成功完成安装。 | |是 |否 |
| 6 |Web 代理 |如果 Web 代理配置将 HTTPS 作为指定的协议，则设备到服务通信将受到影响，并且设备将进入脱机状态。 在此过程中会生成支持包，从而耗用设备上的大量资源。 |请确保 Web 代理 URL 将 HTTP 作为指定的协议。 有关详细信息，请转至[配置设备的 Web 代理](storsimple-configure-web-proxy.md)。 |是 |否 |
| 7 |Web 代理 |如果在注册的设备上配置并启用 Web 代理，将需要重新启动设备上的主动控制器。 | |是 |否 |
| 8 |云高延迟和高 I/O 工作负载 |当 StorSimple 设备同时遇到非常高的云延迟（秒级）和高 I/O 工作负载情况时，设备卷将进入降级状态，并且 I/O 可能会出现故障，发生“设备未就绪”错误。 |需要手动重新启动设备控制器或执行设备故障转移，才可以从这种情况中恢复。 |是 |否 |
| 9 |Azure PowerShell |使用 StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** 选择第一个对象以便创建新的 **VolumeContainer** 对象时，该 cmdlet 将返回所有对象。 |将该 cmdlet 放在括号中，如下所示：**(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |是 |是 |
| 10 |迁移 |当传递多个卷容器进行迁移时，只有第一个卷容器的最新备份的 ETA 准确。 此外，在迁移第一个卷容器中的前 4 个备份后，将开始并行迁移。 |建议一次迁移一个卷容器。 |是 |否 |
| 11 |迁移 |还原后，不会将卷添加到备份策略或虚拟磁盘组。 |需要将这些卷添加到备份策略以创建备份。 |是 |是 |
| 12 |迁移 |迁移完成后，5000/7000 系列设备不得访问已迁移的数据容器。 |建议在迁移完成并提交之后删除迁移的数据容器。 |是 |否 |
| 13 |克隆和 DR |运行 Update 1 的 StorSimple 设备不能对运行 Update 1 前的软件的设备克隆或执行灾难恢复。 |需要将目标设备更新为更新 1 以允许这些操作 |是 |是 |
| 14 |迁移 |当卷组没有关联的卷时，5000-7000 系列设备上用于迁移的配置备份可能会出现故障。 |删除所有不含关联卷的空卷组，并重新配置备份。 |是 |否 |

## <a name="physical-device-updates-in-update-12"></a>Update 1.2 中的物理设备更新
如果修补程序 Update 1.2 应用于运行 Update 1 之前版本的物理设备，软件版本将更改为 6.3.9600.17584。

## <a name="controller-and-firmware-updates-in-update-12"></a>Update 1.2 中的控制器和固件更新
此版本将更新设备上的驱动程序和磁盘固件。

* 有关 SAS 控制器更新的详细信息，请参阅[适用于 Microsoft Azure StorSimple 设备中的 LSI SAS 控制器的 Update 1](https://support.microsoft.com/kb/3043005)。 
* 有关磁盘固件更新的详细信息，请参阅[适用于 Microsoft Azure StorSimple 设备的磁盘固件 Update 1](https://support.microsoft.com/kb/3063416)。

## <a name="virtual-device-updates-in-update-12"></a>Update 1.2 中的虚拟设备更新
此更新不能应用于虚拟设备。 将需要新建虚拟设备。 

## <a name="next-steps"></a>后续步骤
* [在设备上安装 Update 1.2](storsimple-install-update-1.md)。


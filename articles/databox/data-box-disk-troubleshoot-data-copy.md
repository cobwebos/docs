---
title: Azure Data Box 磁盘故障排除数据将复制问题 |Microsoft Docs
description: 介绍如何使用日志的 Azure Data Box 磁盘中的数据复制期间出现的问题进行疑难解答。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148343"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>对 Azure Data Box 磁盘中的数据复制问题进行故障排除

本文适用于 Microsoft Azure Data Box 磁盘，并介绍了如何将数据复制到磁盘时，将显示任何问题进行疑难解答。 使用拆分复制工具时，本文还介绍了问题。


## <a name="data-copy-issues-when-using-a-linux-system"></a>数据复制问题时使用的是 Linux 系统

本部分详细介绍一些使用 Linux 客户端将数据复制到磁盘时所面临的最常见问题。

### <a name="issue-drive-getting-mounted-as-read-only"></a>问题：将驱动器装载为只读
 
**原因** 

这可能是由于文件系统不干净造成。

将驱动器重新装载为读写不适用于 Data Box Disk。 dislocker 解密的驱动器不支持此方案。 你可能已使用以下命令成功重新装载设备：

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

虽然重新装载成功，但不会保留数据。

**解决方法**

在 Linux 系统上执行以下步骤：

1. 为 ntfsfix 实用程序安装 `ntfsprogs` 包。
2. 卸载解锁工具为驱动器提供的装入点。 装入点的数量因驱动器而异。

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. 在相应的路径上运行 `ntfsfix`。 突出显示的数目应与步骤 2 相同。

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. 运行以下命令以删除可能导致装载问题的休眠元数据。

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. 执行干净卸载。

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. 执行干净解锁和装载。
7. 通过写入文件来测试装入点。
8. 卸载并重新装载以验证文件持久性。
9. 继续复制数据。
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>问题：复制后数据不存在时出错
 
**原因** 

如果看到驱动器在卸载后没有数据（尽管数据已复制到其中），则可能在将驱动器装载为只读后将驱动器重新装载为读写。

**解决方法**
 
如果是这种情况，请参阅[将驱动器装载为只读](#issue-drive-getting-mounted-as-read-only)的解决方法。

如果不是这种情况，请从具有 Data Box Disk 解锁工具的文件夹中复制日志，然后[联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。


## <a name="data-box-disk-split-copy-tool-errors"></a>Data Box 磁盘拆分复制工具错误

下表总结了当使用拆分复制工具将数据拆分的多个磁盘时，出现的问题。

|错误消息/警告 |建议 |
|---------|---------|
|[信息] 正在检索卷: m 的 BitLocker 密码 <br>[错误] 检索卷 m: 的 BitLocker 密钥时捕获到异常<br> 序列未包含任何元素。|如果目标 Data Box 磁盘处于脱机状态，则会引发此错误。 <br> 使用 `diskmgmt.msc` 工具将磁盘联机。|
|[错误]引发异常：WMI 操作失败：<br> Method=UnlockWithNumericalPassword，ReturnValue=2150694965， <br>Win32Message=所提供的恢复密码的格式无效。 <br>BitLocker 恢复密码有 48 位。 <br>请验证恢复密码的格式是否正确，然后重试。|使用 Data Box 磁盘解锁工具首先解锁磁盘，然后重试该命令。 有关详细信息，请转到 <li> [为 Windows 客户端解锁 Data Box 磁盘。](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) </li><li> [为 Linux 客户端解锁 Data Box 磁盘。](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) </li>|
|[错误]引发异常：目标驱动器上存在 DriveManifest.xml 文件。 <br> 这表明可能已使用不同的日志文件准备了目标驱动器。 <br>若要向同一驱动器添加更多数据，请使用之前的日志文件。 若要删除现有数据并重复使用新的导入作业的目标驱动器，请删除*DriveManifest.xml*的驱动器上。 使用新的日志文件重新运行此命令。| 当尝试将同一组驱动器用于多个导入会话时会收到此错误。 <br> 将一组驱动器仅用于一个拆分和复制会话。|
|[错误]引发异常：CopySessionId importdata-sept-test-1 引用了以前的复制会话，无法将其重复用于新的复制会话。|当尝试为新作业使用与以前成功完成的作业相同的名称时，会报告此错误。<br> 为新作业分配唯一的名称。|
|[信息] 目标文件或目录名称超出了 NTFS 长度限制。 |当目标文件因为文件路径太长而被重命名时，会报告此消息。<br> 修改 `config.json` 文件中的 disposition 选项来控制此行为。|
|[错误]引发异常：错误的 JSON 转义序列。 |当 Config.json 具有无效格式时，会报告此消息。 <br> 在保存文件之前使用 [JSONlint](https://jsonlint.com/) 验证 `config.json`。|


## <a name="next-steps"></a>后续步骤

- 了解如何[验证工具问题进行故障排除](data-box-disk-troubleshoot.md)。

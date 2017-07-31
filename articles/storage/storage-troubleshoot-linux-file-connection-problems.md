---
title: "在 Linux 中排查 Azure 文件存储问题 | Microsoft Docs"
description: "在 Linux 中排查 Azure 文件存储问题"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 62cd62ec3a2900f06acacc0852a48b5e3ff1c8cd
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="troubleshoot-azure-file-storage-problems-in-linux"></a>在 Linux 中排查 Azure 文件存储问题

本文列出了从 Linux 客户端进行连接时，与 Microsoft Azure 文件存储相关的常见问题。 此外，还提供了这些问题的可能原因和解决方法。

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>尝试打开文件时出现“[权限被拒绝] 超出磁盘配额”

在 Linux 中，将出现类似于下面的错误消息：

**<filename> [权限被拒绝] 超出磁盘配额**

### <a name="cause"></a>原因

已达到文件允许的并发打开句柄数上限。

### <a name="solution"></a>解决方案

关闭一些句柄，减少并发打开句柄的数量，然后重试操作。 有关详细信息，请参阅 [Microsoft Azure 存储性能和可伸缩性清单](storage-performance-checklist.md)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-file-storage-in-linux"></a>在 Linux 中将文件复制到 Azure 文件存储以及从中复制文件时速度缓慢

-   如果没有特定的最低 I/O 大小要求，建议 I/O 大小为 1 MB 以实现最佳性能。
-   如果知道通过写入扩展的最终文件大小，并且文件上尚未写入的结尾包含零时软件不会出现兼容性问题，请提前设置文件大小，而不是使每次写入都成为扩展写入。
-   使用正确的复制方法：
    -   使用 [AZCopy](storage-use-azcopy.md#file-copy) 在两个文件共享之间传输任何内容。
    -   在本地计算机上的文件共享之间使用 [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)。

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>由于重新连接超时，出现“装载错误(112): 主机已关闭”

如果客户端长时间处于空闲状态，Linux 客户端上会发生“112”装载错误。 长时间空闲后，客户端将断开连接，并发生连接超时。  

### <a name="cause"></a>原因

连接可能出于以下原因而进入空闲状态：

-   发生通信故障，导致使用默认“soft”装载选项时阻止 TCP 与服务器重新建立连接。
-   最近的重新连接修复程序未在旧内核中提供

### <a name="solution"></a>解决方案

此 Linux 内核中重新连接的问题现已在实施以下更改的过程中得到修复

- [修复重新连接以在 socket 重新连接后缩短 smb3 会话的重新连接延迟时间](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
-   [socket 重新连接后立即调用 echo 服务](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
-   [CIFS：修复重新连接期间潜在的内存损坏](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
-   [CIFS：修复重新连接期间潜在的互斥双锁（适用于内核 v4.9 及更高版本）](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

但是，这些更改可能尚未移植到所有的 Linux 发行版。 此修复及其他重新连接修复已在以下热门 Linux 内核中执行：4.4.40、4.8.16 和 4.9.1。 可以通过升级到这些建议的内核版本之一来获取此修复程序。

### <a name="workaround"></a>解决方法

可以通过指定硬装载来解决此问题。 这会强制客户端等待，直到建立连接或显式中断为止，可用于避免由于网络超时而引起的错误。 但是，此解决方法可能会导致无限期等待。 请准备好在必要情况下停止连接。

如果无法升级到最新的内核版本，可通过将每隔 30 秒或更少的时间间隔便会对其进行写入操作的文件保留在 Azure 文件共享中来解决此问题。 这必须是一个写入操作，例如在文件上重写创建或修改的日期。 否则，可能会得到缓存的结果，并且操作可能不会触发重新连接。

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-file-storage-by-using-smb-30"></a>使用 SMB 3.0 装载 Azure 文件存储时出现“装载错误(115): 操作正在进行”

### <a name="cause"></a>原因

某些 Linux 发行版尚不支持 SMB 3.0 中的加密功能，如果用户尝试使用 SMB 3.0 装载 Azure 文件存储，可能会由于缺少功能而收到“115”错误消息。

### <a name="solution"></a>解决方案

在 4.11 内核中引入了适用于 Linux 的 SMB 3.0 加密功能。 此功能允许从本地或不同 Azure 区域装载 Azure 文件共享。 在发布时，此功能已向后移植到 Ubuntu 17.04 和 Ubuntu 16.10。 如果 Linux SMB 客户端不支持加密，请使用 SMB 2.1 从文件存储帐户所在的同一数据中心上的 Azure Linux VM 装载 Azure 文件存储。

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM 上装载的 Azure 文件共享性能缓慢

### <a name="cause"></a>原因

性能缓慢的一个可能原因是禁用了缓存。

### <a name="solution"></a>解决方案

若要检查是否禁用了缓存，请查找 **cache=** 条目。 

**cache=none** 表示已禁用缓存。  使用默认的装载命令重新装载共享，或者显式添加 **cache=strict** 选项到装载命令中，确保默认缓存或“strict”缓存模式已启用。

在某些情况下，**serverino** 装载选项可能会导致 **ls** 命令针对每个目录条目运行 stat。 列出大型目录时，此行为会导致性能下降。 可在 **/etc/fstab** 条目中检查装载选项：

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

还可以通过运行 **sudo mount | grep cifs** 命令并检查其输出，来检查所用的选项是否正确，如以下示例输出所示：

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

如果不存在 **cache=strict** 或 **serverino** 选项，请通过运行[文档](storage-how-to-use-files-linux.md)中的装载命令卸载并再次装载 Azure 文件存储。 然后重新检查 **/etc/fstab** 条目是否具有正确选项。

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>将文件从 Windows 复制到 Linux 时丢失时间戳

在 Linux/Unix 平台上，如果文件 1 和文件 2 由不同的用户拥有，则 **cp-p** 命令将会失败。

### <a name="cause"></a>原因

COPYFILE 中的强制标志 **f** 导致在 Unix 上执行 **cp -p -f**。 此外，此命令无法保存你不拥有的文件的时间戳。

### <a name="workaround"></a>解决方法

使用存储帐户用户来复制文件：

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。


---
title: 在 Linux 中排查 Azure 文件问题 | Microsoft Docs
description: 在 Linux 中排查 Azure 文件问题
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9c08cd52bba6391660bc5f28e5db2dbec1126951
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118707"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>在 Linux 中排查 Azure 文件问题

本文列出了从 Linux 客户端进行连接时，与 Azure 文件相关的常见问题。 此外，还提供了这些问题的可能原因和解决方法。 

除本文中的疑难解答步骤之外，还可使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) 确保 Linux 客户端满足正确的先决条件。 AzFileDiagnostics 自动检测本文中提到的大多数症状。 它有助于设置环境以获得最佳性能。 也可以在 [Azure 文件共享疑难解答](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到此信息。 疑难解答提供了帮助你解决连接、映射和装载 Azure 文件共享问题的步骤。

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>无法连接或装载 Azure 文件共享

### <a name="cause"></a>原因

造成此问题的常见原因包括：

- 正在使用不兼容的 Linux 分发客户端。 建议使用以下 Linux 分发来连接 Azure 文件共享：

|   | SMB 2.1 <br>（装载在同一 Azure 区域内的 VM 上） | SMB 3.0 <br>（从本地和跨区域装载） |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- 客户端上未安装 CIFS 实用程序 (cfs-utils)。
- 客户端上未安装最低的 SMB/CIFS 版本 2.1。
- 客户端不支持 SMB 3.0 加密。 上表中提供的 Linux 分发版列表从内部和跨区域的支持装载使用加密。 其他分发要求内核 4.11 及更高版本。
- 试图通过不受支持的 TCP 端口 445 连接到存储帐户。
- 试图从 Azure VM 连接到 Azure 文件共享，而该 VM 并非与存储帐户处于同一区域。
- 如果在存储帐户上启用了[需要安全转移]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)设置，则 Azure 文件仅允许使用带加密的 SMB 3.0 进行连接。

### <a name="solution"></a>解决方案

若要解决此问题，请使用 [Troubleshooting tool for Azure Files mounting errors on Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089)（用于 Linux 上 Azure 文件装载错误的故障排除工具）。 此工具：

* 有助于验证客户端运行环境。
* 可检测导致 Azure 文件访问失败的不兼容客户端配置。
* 可提供自我修复的说明性指导。
* 可收集诊断跟踪。

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>装载 Azure 文件共享时出现“装载错误(13):权限被拒绝”

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：信道未加密

出于安全考虑，如果信道未加密，且连接尝试并非来自 Azure 文件共享所在的同一数据中心，将阻止连接到 Azure 文件共享。 如果在存储帐户中启用[需要安全传输](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)设置，则还可以阻止同一数据中心中未加密的连接。 仅当用户的客户端 OS 支持 SMB 加密时，才提供加密的信道。

若要了解详细信息，请参阅[使用 Linux 和 cifs-utils 包装载 Azure 文件共享的先决条件](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package)。 

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

1. 从支持 SMB 加密的客户端进行连接，或者从用于 Azure 文件共享的 Azure 存储帐户所在数据中心内的虚拟机进行连接。
2. 如果客户端不支持 SMB 加密，请验证是否已在存储帐户上禁用[需要安全传输](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)设置。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存储帐户上启用了虚拟网络或防火墙规则 

如果在存储帐户上配置了虚拟网络 (VNET) 和防火墙规则，则将拒绝访问网络流量，除非允许客户端 IP 地址或虚拟网络访问。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”  。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>尝试打开文件时出现“[权限被拒绝] 超出磁盘配额”

在 Linux 中，将出现类似于下面的错误消息：

**\<文件名 > [权限被拒绝] 超出磁盘配额**

### <a name="cause"></a>原因

已达到文件允许的并发打开句柄数上限。

单个文件有 2000 个打开句柄配额。 当你拥有 2000 个打开句柄时，会显示一条错误消息，指示已达到此配额。

### <a name="solution"></a>解决方案

关闭一些句柄，减少并发打开句柄的数量，然后重试操作。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>在 Linux 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

- 如果没有特定的最低 I/O 大小要求，建议 I/O 大小为 1 MiB 以实现最佳性能。
- 如果知道通过写入扩展的最终文件大小，并且文件上尚未写入的结尾包含零时软件不会出现兼容性问题，请提前设置文件大小，而不是使每次写入都成为扩展写入。
- 使用正确的复制方法：
    - 使用 [AZCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 在两个文件共享之间传输任何内容。
    - 通过并行使用 cp 可以提高复制速度，线程数取决于你的用例和工作负荷。 此示例使用六个： `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`。
    - 打开源第三方工具，如：
        - [GNU 并行](http://www.gnu.org/software/parallel/)。
        - [Fpart](https://github.com/martymac/fpart) -对文件进行排序，并将其打包到分区。
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -使用 Fpart 和复制工具来生成多个实例，若要将数据从 src_dir 迁移到 dst_url。
        - [多](https://github.com/pkolano/mutil)-根据 GNU coreutils 多线程的 cp 和 md5sum。

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>使用 SMB 3.0 装载 Azure 文件时出现“装载错误(115): 操作正在进行”

### <a name="cause"></a>原因

某些 Linux 分发尚不支持 SMB 3.0 中的加密功能。 如果用户尝试使用 SMB 3.0 装载 Azure 文件，可能会由于缺少功能而收到“115”错误消息。 只有在使用 Ubuntu 16.04 或更高版本时，才支持具有完全加密的 SMB 3.0。

### <a name="solution"></a>解决方案

4\.11 内核中引入了适用于 Linux 的 SMB 3.0 加密功能。 使用此功能可从本地或不同 Azure 区域装载 Azure 文件共享。 此功能包含在中列出的 Linux 分发版[最小建议相应装载功能 （SMB 版本 2.1 或 SMB 3.0 版） 的版本](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30)。 其他分发要求内核 4.11 及更高版本。

如果 Linux SMB 客户端不支持加密，请使用 SMB 2.1 从文件共享所在的同一数据中心上的 Azure Linux VM 装载 Azure 文件。 验证是否已在存储帐户中禁用[需要安全传输]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)设置。 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>"授权失败"错误时浏览到在门户中的 Azure 文件共享

浏览到门户中的 Azure 文件共享时，可能会收到以下错误：

授权失败  
你无权访问

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 1：你的用户帐户无权访问该存储帐户

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

浏览到Azure文件共享所在的存储帐户，单击“访问控制(IAM)”，确保你的用户帐户有权访问该存储帐户  。 若要了解详细信息，请参阅[如何使用基于角色的访问控制 (RBAC) 来保护存储帐户](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac)。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存储帐户上启用了虚拟网络或防火墙规则

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”  。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM 上装载的 Azure 文件共享性能缓慢

### <a name="cause-1-caching"></a>原因 1：正在缓存

性能缓慢的一个可能原因是禁用了缓存。 缓存可以是如果您重复访问的文件，否则，它可以是一项开销非常有用。 检查是否禁用它之前使用缓存。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

若要检查是否禁用了缓存，请查找 **cache=** 条目。

**cache=none** 表示已禁用缓存。 使用默认的装载命令重新装载共享，或者显式添加 **cache=strict** 选项到装载命令中，确保默认缓存或“strict”缓存模式已启用。

在某些情况下，**serverino** 装载选项可能会导致 **ls** 命令针对每个目录条目运行 stat。 要列出大型目录时，此行为将导致性能下降。 可在 **/etc/fstab** 条目中检查装载选项：

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

还可以通过运行 sudo mount | grep cifs 命令并检查其输出，检查所用的选项是否正确  。 下面是示例输出：

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

如果不存在 cache=strict 或 serverino 选项，请通过运行[文档](../storage-how-to-use-files-linux.md)中的装载命令卸载并再次装载 Azure 文件   。 然后重新检查 **/etc/fstab** 条目是否具有正确选项。

### <a name="cause-2-throttling"></a>原因 2：限制

它是可能遇到限制，并且你的请求发送到队列。 您可以通过利用对此进行验证[Azure Monitor 中的 Azure 存储指标](../common/storage-metrics-in-azure-monitor.md)。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

确保你的应用程序内[Azure 文件规模目标](storage-files-scale-targets.md#azure-files-scale-targets)。

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>将文件从 Windows 复制到 Linux 时丢失时间戳

在 Linux/Unix 平台上，如果文件 1 和文件 2 由不同的用户拥有，则 cp-p 命令将失败  。

### <a name="cause"></a>原因

COPYFILE 中的强制标志 **f** 导致在 Unix 上执行 **cp -p -f**。 此外，此命令无法保存你不拥有的文件的时间戳。

### <a name="workaround"></a>解决方法

使用存储帐户用户来复制文件：

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: 无法访问 '&lt;path&gt;':输入/输出错误

尝试使用 ls 命令列出 Azure 文件共享中的文件时，该命令在列出文件时挂起。 收到以下错误：

**ls: 无法访问'&lt;path&gt;':输入/输出错误**


### <a name="solution"></a>解决方案
将 Linux 内核升级到以下可解决此问题的版本：

- 4.4.87+
- 4.9.48+
- 4.12.11+
- 4\.13 或更高的所有版本

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>无法创建符号链接 - ln: 未能创建符号链接 't':操作不受支持

### <a name="cause"></a>原因
默认情况下，使用 CIFS 在 Linux 上装载 Azure 文件共享不会启用符号链接的支持。 出现如下错误：
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>解决方案
Linux CIFS 客户端不支持通过 SSMB 2 或 3 协议创建 Windows 样式符号链接。 Linux 客户端目前支持使用称作 [Minshall+French 符号链接](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks)的另一种样式的符号链接来执行创建和跟踪操作。 需要符号链接的客户可以使用“mfsymlinks”装载选项。 我们推建议使用“mfsymlinks”，因为这也是 Macs 使用的格式。

若要使用符号链接，请将以下代码添加到 CIFS 装载命令的末尾：

```
,mfsymlinks
```

因此，命令如下所示：

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

然后，可以按照 [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers) 上的建议创建符号链接。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

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
- [socket 重新连接后立即调用 echo 服务](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS：修复重新连接期间潜在的内存损坏](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS：修复重新连接期间潜在的互斥双锁（适用于内核 v4.9 及更高版本）](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

但是，这些更改可能尚未移植到所有的 Linux 发行版。 中可以找到此修补程序和其他重新连接修复[最小建议相应装载功能 （SMB 版本 2.1 或 SMB 3.0 版） 的版本](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30)一部分[使用 Azure 文件和 Linux](storage-how-to-use-files-linux.md)一文。 可以通过升级到这些建议的内核版本之一来获取此修复程序。

### <a name="workaround"></a>解决方法

可以通过指定硬装载来解决此问题。 在连接建立或连接明确中断前，硬装载强制客户端等待。 可以借此防止由于网络超时而导致的错误。 但是，此解决方法可能会导致无限期等待。 请准备好在必要情况下停止连接。

如果无法升级到最新的内核版本，可通过将每隔 30 秒或更少的时间间隔便会对其进行写入操作的文件保留在 Azure 文件共享中来解决此问题。 这必须是一个写入操作，例如在文件上重写创建或修改的日期。 否则，可能会得到缓存的结果，并且操作可能不会触发重新连接。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。

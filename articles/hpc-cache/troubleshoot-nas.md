---
title: 排除 Azure HPC 缓存 NFS 存储目标
description: 避免和修复创建 NFS 存储目标时可能导致故障的配置错误和其他问题的提示
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rohogue
ms.openlocfilehash: 0a24530810a448a713c01efbc8933b9f22d15b3b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536363"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>解决 NAS 配置和 NFS 存储目标问题

本文提供了一些常见的配置错误和其他问题的解决方案，这些问题可能会阻止 Azure HPC 缓存将 NFS 存储系统添加为存储目标。

本文包含有关如何检查端口以及如何启用对 NAS 系统的根访问的详细信息。 它还包含有关可能导致 NFS 存储目标创建失败的不太常见的问题的详细信息。

> [!TIP]
> 在使用本指南之前，请阅读[NFS 存储目标的先决条件](hpc-cache-prereqs.md#nfs-storage-requirements)。

如果此处未包含您的问题解决方案，请[打开支持票证](hpc-cache-support-ticket.md)，以便 Microsoft 服务和支持部门可以与您合作调查和解决问题。

## <a name="check-port-settings"></a>检查端口设置

Azure HPC 缓存需要对后端 NAS 存储系统上的多个 UDP/TCP 端口的读/写访问。 确保这些端口在 NAS 系统上可访问，并且允许通过存储系统和缓存子网之间的任何防火墙访问这些端口。 您可能需要与数据中心的防火墙和网络管理员合作以验证此配置。

来自不同供应商的存储系统端口不同，因此在设置存储目标时请检查系统的要求。

通常，缓存需要访问这些端口：

| 协议 | 端口  | 服务  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | 恩洛克姆格 |
| TCP/UDP  | 4046  | 已安装   |
| TCP/UDP  | 4047  | status   |

要了解系统所需的特定端口，请使用以下``rpcinfo``命令。 下面的此命令列出了端口，并在表中格式化相关结果。 （使用系统的 IP 地址代替 *<storage_IP>* 术语。

可以从安装了 NFS 基础结构的任何 Linux 客户端发出此命令。 如果在群集子网中使用客户端，它还可以帮助验证子网和存储系统之间的连接。

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

确保``rpcinfo``查询返回的所有端口都允许来自 Azure HPC 缓存子网的无限制流量。

在 NAS 本身以及存储系统和缓存子网之间的任何防火墙上检查这些设置。

## <a name="check-root-access"></a>检查根访问

Azure HPC 缓存需要访问存储系统的导出以创建存储目标。 具体而言，它将导出装载为用户 ID 0。

不同的存储系统使用不同的方法来启用此访问：

* Linux 服务器通常``no_root_squash``添加到 中的``/etc/exports``导出路径。
* NetApp 和 EMC 系统通常使用与特定 IP 地址或网络关联的导出规则来控制访问。

如果使用导出规则，请记住缓存可以使用与缓存子网的多个不同的 IP 地址。 允许从各种可能的子网 IP 地址进行访问。

> [!NOTE]
> 默认情况下，Azure HPC 缓存会挤压根访问。 阅读[配置其他缓存设置](configuration.md#configure-root-squash)的详细信息。

与 NAS 存储供应商合作，为缓存启用正确的访问级别。

### <a name="allow-root-access-on-directory-paths"></a>允许在目录路径上进行根访问
<!-- linked in prereqs article -->

对于导出分层目录的 NAS 系统，Azure HPC 缓存需要对每个导出级别的根访问。

例如，系统可能会显示以下三个导出：

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

导出``/ifs/accounting/payroll``是 的``/ifs/accounting``子项，本身就是``/ifs/accounting``的``/ifs``子项。

如果将``payroll``导出添加为 HPC 缓存存储目标，则缓存实际上会从那里装载``/ifs/``和访问工资单目录。 因此，Azure HPC 缓存需要``/ifs``根访问才能访问``/ifs/accounting/payroll``导出。

此要求与缓存索引文件的方式有关，并且使用存储系统提供的文件句柄避免文件冲突。

如果从不同的导出检索该文件，则具有分层导出的 NAS 系统可以为同一文件提供不同的文件句柄。 例如，客户端可以装载``/ifs/accounting``和访问该文件。 ``payroll/2011.txt`` 另一个客户端``/ifs/accounting/payroll``装载和访问该文件``2011.txt``。 根据存储系统分配文件句柄的方式，这两个客户端可能会收到具有不同文件句柄的同一文件（一个对于``<mount2>/payroll/2011.txt``，一个``<mount3>/2011.txt``用于 ）。

后端存储系统保留文件句柄的内部别名，但 Azure HPC 缓存无法判断其索引中哪个文件句柄引用同一项。 因此，缓存可能为同一文件缓存不同的写入，并且错误地应用更改，因为它不知道它们是同一文件。

为了避免多个导出中的文件可能的文件冲突，Azure HPC Cache 会自动在路径中装载最浅的可用导出（``/ifs``在示例中），并使用该导出中提供的文件句柄。 如果多个导出使用相同的基本路径，Azure HPC 缓存需要对该路径的根访问权限。

## <a name="enable-export-listing"></a>启用导出列表
<!-- link in prereqs article -->

当 Azure HPC 缓存查询其导出时，NAS 必须列出其导出。

在大多数 NFS 存储系统上，您可以通过从 Linux 客户端发送以下查询来测试此情况：``showmount -e <storage IP address>``

如果可能，请使用与缓存相同的虚拟网络中的 Linux 客户端。

如果该命令未列出导出，则缓存将难以连接到存储系统。 与 NAS 供应商合作，启用导出列表。

## <a name="adjust-vpn-packet-size-restrictions"></a>调整 VPN 数据包大小限制
<!-- link in prereqs article and configuration article -->

如果在缓存和 NAS 设备之间具有 VPN，则 VPN 可能会阻止全尺寸 1500 字节以太网数据包。 如果 NAS 和 Azure HPC 缓存实例之间的大型交换未完成，但较小的更新按预期工作，则可能存在此问题。

除非您知道 VPN 配置的详细信息，否则无法判断您的系统是否有此问题的简单方法。 下面是一些可以帮助您检查此问题的方法。

* 使用 VPN 两侧的数据包嗅探器检测成功传输的数据包。
* 如果您的 VPN 允许 ping 命令，则可以测试发送全尺寸数据包。

  使用这些选项将 VPN 上的 ping 命令运行到 NAS。 （使用存储系统的 IP 地址代替 *<storage_IP>* 值。

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  以下是命令中的选项：

  * ``-M do``- 不要碎片
  * ``-c 1``- 只发送一个数据包
  * ``-s 1472``- 将有效负载的大小设置为 1472 字节。 这是考虑到以太网开销后 1500 字节数据包的最大大小负载。

  成功响应如下所示：

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  如果 ping 以 1472 字节失败，则可能存在数据包大小问题。

要解决此问题，您可能需要配置 VPN 上的 MSS 夹紧功能，以使远程系统正确检测最大帧大小。 阅读[VPN 网关 IPsec/IKE 参数文档](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)以了解更多信息。

在某些情况下，将 Azure HPC 缓存的 MTU 设置更改为 1400 可能会有所帮助。 但是，如果将 MTU 限制在缓存上，则还必须限制与缓存交互的客户端和后端存储系统的 MTU 设置。 阅读[配置其他 Azure HPC 缓存设置](configuration.md#adjust-mtu-value)以了解详细信息。

## <a name="check-for-acl-security-style"></a>检查 ACL 安全样式

某些 NAS 系统使用混合安全样式，将访问控制列表 （ACL） 与传统 POSIX 或 UNIX 安全性相结合。

如果您的系统将其安全样式报告为 UNIX 或 POSIX，但不包括首字母缩写词"ACL"，则此问题不会影响您。

对于使用 ACL 的系统，Azure HPC 缓存需要跟踪其他用户特定的值，以便控制文件访问。 这是通过启用访问缓存来完成的。 没有面向用户的控件来打开访问缓存，但您可以打开支持票证，请求为缓存系统上受影响的存储目标启用该支持票证。

## <a name="next-steps"></a>后续步骤

如果您有本文未解决的问题，[则打开支持票证](hpc-cache-support-ticket.md)以获得专家帮助。

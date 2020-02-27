---
title: 排查 Azure HPC 缓存 NFS 存储目标问题
description: 避免和修复在创建 NFS 存储目标时可能导致故障的配置错误和其他问题的提示
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652082"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>排查 NAS 配置和 NFS 存储目标问题

本文提供了一些常见配置错误和其他问题的解决方案，这些问题可能会阻止 Azure HPC 缓存将 NFS 存储系统添加为存储目标。

本文包含有关如何检查端口以及如何启用对 NAS 系统的根访问的详细信息。 它还包括有关可能导致 NFS 存储目标创建失败的常见问题的详细信息。

> [!TIP]
> 使用本指南之前，请先阅读[NFS 存储目标的先决条件](hpc-cache-prereqs.md#nfs-storage-requirements)。

如果此处未包含您的问题的解决方案，请[打开支持票证](hpc-cache-support-ticket.md)，以便 Microsoft 服务和支持人员与您合作来调查并解决问题。

## <a name="check-port-settings"></a>检查端口设置

Azure HPC 缓存需要对后端 NAS 存储系统上的几个 UDP/TCP 端口具有读/写访问权限。 请确保这些端口可在 NAS 系统上访问，并且还允许通过存储系统与缓存子网之间的任何防火墙对这些端口进行通信。 你可能需要与数据中心的防火墙和网络管理员合作来验证此配置。

不同供应商的存储系统端口不同，因此请在设置存储目标时检查系统要求。

通常，缓存需要访问以下端口：

| 协议 | 端口  | 服务  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | status   |

若要了解系统所需的特定端口，请使用以下 ``rpcinfo`` 命令。 以下命令列出了这些端口，并在表中设置相关结果的格式。 （使用系统的 IP 地址代替 *< storage_IP >* 术语。）

你可以从安装了 NFS 基础结构的任何 Linux 客户端发出此命令。 如果在群集子网中使用客户端，它还可以帮助验证子网和存储系统之间的连接。

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

请确保 ``rpcinfo`` 查询返回的所有端口都允许来自 Azure HPC 缓存的子网的不受限制的流量。

在 NAS 本身以及存储系统与缓存子网之间的任何防火墙上检查这些设置。

## <a name="check-root-access"></a>检查根访问权限

Azure HPC 缓存需要访问存储系统的导出才能创建存储目标。 具体而言，它将导出作为用户 ID 0 进行装载。

不同的存储系统使用不同的方法来实现此访问：

* 通常，Linux 服务器将 ``no_root_squash`` 添加到 ``/etc/exports``中的导出路径。
* NetApp 和 EMC 系统通常使用绑定到特定 IP 地址或网络的导出规则来控制访问。

如果使用导出规则，请记住缓存可以使用缓存子网中的多个不同的 IP 地址。 允许从可能的子网 IP 地址的完整范围进行访问。

与 NAS 存储供应商合作，为缓存启用适当的访问级别。

### <a name="allow-root-access-on-directory-paths"></a>允许对目录路径进行根访问
<!-- linked in prereqs article -->

对于导出层次结构目录的 NAS 系统，Azure HPC 缓存需要对每个导出级别的根访问权限。

例如，系统可能会显示三个导出，如下所示：

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

导出 ``/ifs/accounting/payroll`` 是 ``/ifs/accounting``的子，``/ifs/accounting`` 本身是 ``/ifs``的子元素。

如果将 ``payroll`` 导出作为 HPC 缓存存储目标添加，则缓存实际上会装载 ``/ifs/`` 并从该目标访问工资单目录。 因此，Azure HPC 缓存需要对 ``/ifs`` 的根访问权限才能访问 ``/ifs/accounting/payroll`` 导出。

此要求与缓存对文件进行索引的方式相关，并使用存储系统提供的文件句柄避免了文件冲突。

如果文件是从不同的导出中检索的，则具有分层导出的 NAS 系统可以为同一文件提供不同的文件句柄。 例如，客户端可以装载 ``/ifs/accounting`` 并访问 ``payroll/2011.txt``的文件。 其他客户端装载 ``/ifs/accounting/payroll`` 并访问 ``2011.txt``的文件。 根据存储系统分配文件句柄的方式，这两个客户端可能会收到与不同文件句柄相同的文件（一个用于 ``<mount2>/payroll/2011.txt``，一个用于 ``<mount3>/2011.txt``）。

后端存储系统保留文件句柄的内部别名，但 Azure HPC 缓存无法判断其索引中哪些文件句柄引用同一项目。 因此，缓存可能会为同一文件缓存不同的写入，并不能正确应用更改，因为它不知道它们是同一文件。

为了避免多个导出中的文件发生此可能的文件冲突，Azure HPC 缓存会自动在路径中装载 shallowest 可用的导出（在示例中为``/ifs``），并使用该导出提供的文件句柄。 如果多个导出使用相同的基路径，则 Azure HPC 缓存需要对该路径的根访问权限。

## <a name="enable-export-listing"></a>启用导出列表
<!-- link in prereqs article -->

在 Azure HPC 缓存查询时，NAS 必须列出其导出。

在大多数 NFS 存储系统上，可以通过从 Linux 客户端发送以下查询来对此进行测试： ``showmount -e <storage IP address>``

如果可能，请使用与缓存位于同一虚拟网络中的 Linux 客户端。

如果该命令未列出导出，则缓存在连接到存储系统时将会出现问题。 请与 NAS 供应商合作，启用导出列表。

## <a name="adjust-vpn-packet-size-restrictions"></a>调整 VPN 数据包大小限制
<!-- link in prereqs article -->

如果在缓存和 NAS 设备之间有 VPN，VPN 可能会阻止全尺寸的1500字节以太网数据包。 如果在 NAS 和 Azure HPC 缓存实例之间进行较大的交换不能完成，但较小的更新会按预期工作，则可能会遇到此问题。

除非您知道 VPN 配置的详细信息，否则不能使用简单的方法来判断您的系统是否出现此问题。 下面是可以帮助您检查此问题的几种方法。

* 使用 VPN 两侧的数据包嗅探检测成功传输的数据包。
* 如果 VPN 允许 ping 命令，则可以测试发送全尺寸的数据包。

  使用这些选项在 NAS 上运行 ping 命令。 （使用存储系统的 IP 地址代替 *< storage_IP >* 的值。）

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  以下是命令中的选项：

  * ``-M do``-不分段
  * ``-c 1``-只发送一个数据包
  * ``-s 1472``-将有效负载的大小设置为1472字节。 这是在为以太网开销进行记帐后1500字节数据包的最大负载。

  成功响应如下所示：

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  如果 ping 失败，1472字节，你可能需要在 VPN 上配置 MSS 钳位，以使远程系统正确检测最大帧大小。 有关详细信息，请参阅[VPN 网关 IPsec/IKE 参数文档](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)。

## <a name="check-for-acl-security-style"></a>检查 ACL 安全样式

某些 NAS 系统使用混合安全样式，该样式结合了访问控制列表（Acl）与传统 POSIX 或 UNIX 安全性。

如果系统将其安全样式报告为 UNIX 或 POSIX，而不包含首字母缩写词，此问题不会影响你。

对于使用 Acl 的系统，Azure HPC 缓存需要跟踪其他特定于用户的值才能控制文件访问。 这是通过启用访问缓存来完成的。 没有用于打开访问缓存的面向用户的控件，但你可以打开支持票证，请求在缓存系统上为受影响的存储目标启用此功能。

## <a name="next-steps"></a>后续步骤

如果遇到本文未解决的问题，请[打开支持票证](hpc-cache-support-ticket.md)以获取专家帮助。

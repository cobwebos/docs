---
title: 已知问题 HB 系列和 HC 系列 Vm 的 Azure 虚拟机 |Microsoft Docs
description: 了解如何在 Azure 中的 HB 系列 VM 大小的已知问题。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707779"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB 系列和 HC 系列 VM 的已知问题

使用 HB 系列和 HC 系列 Vm 时，这篇文章提供了最常见的问题和解决方案。

## <a name="dram-on-hb-series"></a>DRAM HB 系列上

HB 系列 Vm 可以在此时间仅公开 228 GB 的 RAM 为来宾虚拟机。 这是因为 Azure 虚拟机监控程序的已知限制来防止页分配给本地 DRAM 的 AMD CCX 的 （NUMA 域） 为来宾 VM 保留。

## <a name="accelerated-networking"></a>加速网络

在此期间，不启用 azure 加速网络，但将我们在预览期间通过。 支持此功能时，我们会通知客户。

## <a name="qp0-access-restriction"></a>qp0 访问限制

若要防止可能会导致安全漏洞，队列对的低级别的硬件访问 0 不可访问来宾 Vm。 这应该只会影响操作通常与管理 ConnectX 5 NIC 相关联，并运行类似于 ibdiagnet，但不是最终用户应用程序本身某些 InfiniBand 诊断。

## <a name="ud-transport"></a>UD 传输

启动时，HB 和 HC 系列不支持动态连接传输 (DCT)。 随着时间的推移，将实现对 DCT 的支持。 支持可靠的连接 (RC) 和不可靠数据报 (UD) 传输。

## <a name="gss-proxy"></a>GSS 代理

GSS 代理可能表现为显著提高性能和响应能力损失与 NFS 一起使用时的 CentOS/RHEL 7.5 中有一个已知的 bug。 这可以降低使用：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>清除缓存

在 HPC 系统中，是通常用于完成作业后下, 一个用户分配相同的节点之前清理内存。 在 Linux 中运行应用程序后可能会发现在缓冲区内存增加，尽管未运行任何应用程序时可减少可用内存。

![命令提示的屏幕截图](./media/known-issues/cache-cleaning-1.png)

使用`numactl -H`将显示哪些 NUMAnode(s) 内存缓冲 （可能是所有）。 在 Linux 中，用户可以清理在三个缓存方法以返回缓冲或缓存内存免费。 您需要是根或具有 sudo 权限。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![命令提示的屏幕截图](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>内核警告

在启动 Linux 下的 HB 系列 VM 时，可能会看到以下内核警告消息。

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

可以忽略此警告。 这是因为 Azure 虚拟机监控程序将随着时间的推移解决的已知限制。

## <a name="next-steps"></a>后续步骤

详细了解如何[高性能计算](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 中。

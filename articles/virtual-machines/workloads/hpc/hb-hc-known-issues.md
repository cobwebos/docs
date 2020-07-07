---
title: HB-ACCT-WC 系列和 HC 系列 Vm 的已知问题-Azure 虚拟机 |Microsoft Docs
description: 了解 Azure 中 HB-ACCT-WC 系列 VM 大小的已知问题。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707779"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB 系列和 HC 系列 VM 的已知问题

本文提供使用 HB-ACCT-WC 系列和 HC 系列 Vm 时最常见的问题和解决方案。

## <a name="dram-on-hb-series"></a>HB-ACCT-WC 系列上的 DRAM

HB-ACCT-WC 系列 Vm 此时只能向来宾 Vm 公开 228 GB 的 RAM。 这是因为 Azure 虚拟机监控程序的已知限制阻止将页面分配到为来宾 VM 保留的 AMD CCX （NUMA 域）的本地 DRAM。

## <a name="accelerated-networking"></a>加速网络

此时未启用 Azure 加速网络，但将在预览期间完成。 当支持此功能时，我们将通知客户。

## <a name="qp0-access-restriction"></a>qp0 访问限制

若要防止可能导致安全漏洞的低级别硬件访问，来宾 Vm 将无法访问队列对0。 这只会影响通常与 ConnectX-5 NIC 的管理相关的操作，并运行一些不受影响的诊断（如 ibdiagnet），而不是最终用户应用程序本身。

## <a name="ud-transport"></a>UD 传输

启动时，HB-ACCT-WC 和 HC 不支持动态连接的传输（DCT）。 将随着时间的推移实现对 DCT 的支持。 支持可靠连接（RC）和不可靠的数据报（UD）传输。

## <a name="gss-proxy"></a>GSS 代理

GSS Proxy 在 CentOS/RHEL 7.5 中有一个已知 bug，在与 NFS 一起使用时，它们可以表现为显著的性能和响应能力。 可以通过以下方式缓解这一点：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>缓存清理

在 HPC 系统上，在为下一个用户分配同一个节点之前，在作业完成之后清理内存通常非常有用。 在 Linux 中运行应用程序后，您可能会发现，当您的缓冲区内存增加时，您的可用内存会减少，而不运行任何应用程序。

![命令提示符屏幕截图](./media/known-issues/cache-cleaning-1.png)

使用 `numactl -H` 将显示使用哪个 NUMAnode 缓冲内存（可能全部）。 在 Linux 中，用户可以通过三种方式清理缓存，以将缓冲或缓存的内存返回到 "免费"。 你需要是根或具有 sudo 权限。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![命令提示符屏幕截图](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>内核警告

在 Linux 下启动 HB-ACCT-WC 系列 VM 时，可能会看到以下内核警告消息。

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

您可以忽略此警告。 这是因为将在一段时间内解决的 Azure 虚拟机监控程序的已知限制。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 中的[高性能计算](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)。

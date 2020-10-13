---
title: 排查 HPC 和 GPU Vm 的已知问题-Azure 虚拟机 |Microsoft Docs
description: 了解排查 Azure 中的 HPC 和 GPU VM 大小的已知问题。
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 52050066aceef50e64c5887efb7b99da0b03f140
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996261"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H 系列和 N 系列 VM 的已知问题

本文提供了使用 [H 系列](../../sizes-hpc.md) 和 [N 系列](../../sizes-gpu.md) HPC 和 GPU vm 时最常见的问题和解决方案。

## <a name="infiniband-driver-installation-on-n-series-vms"></a>N 系列虚拟机上的未感知驱动程序安装

NC24r_v3 和 ND40r_v2 在 NC24r 和 NC24r_v2 未启用 SR-IOV 时启用 SR-IOV。 有关分叉的详细信息，请参阅 [此处](../../sizes-hpc.md#rdma-capable-instances)。
可在启用了 SR-IOV 的 VM 大小和 OFED 驱动程序的情况下配置可 (IB) ，而非 SR-IOV VM 大小需要 ND 驱动程序。 此 IB 支持在 [CentOS-HPC VMIs](configure.md)上适当提供。 对于 Ubuntu，请参阅 [此处的说明](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) ，安装 OFED 和 ND 驱动程序，如 [文档](enable-infiniband.md#vm-images-with-infiniband-drivers)中所述。

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>在 H 系列和 N 系列 Vm 上，通过带有云初始化的虚拟机与 Ubuntu 重复

Ubuntu VM 映像上的云初始化存在已知问题，因为它会尝试打开 IB 接口。 这可能发生在 VM 重新启动或通用化后尝试创建 VM 映像。 VM 启动日志可能会显示错误，如下所示： "正在启动网络服务 .。。RuntimeError：找到了重复的 mac！ "eth1" 和 "ib0" 都具有 mac "。

这是一个已知问题，即在 Ubuntu 上使用云初始化的 "重复 MAC"。 解决方法是：
1) 部署 (Ubuntu 18.04) marketplace VM 映像
2) 安装必要的软件包，以便 [在此处](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) 启用 IB (指令) 
3) 编辑 waagent 以更改 EnableRDMA = y
4) 禁用云初始化中的网络
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) 编辑 netplan 的网络配置文件，该文件由云初始化生成以删除 MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
        ethernets:
        eth0:
            dhcp4: true
        version: 2
    EOF
    ```

## <a name="dram-on-hb-series"></a>HB-ACCT-WC 系列上的 DRAM

HB-ACCT-WC 系列 Vm 此时只能向来宾 Vm 公开 228 GB 的 RAM。 这是因为 Azure 虚拟机监控程序的已知限制阻止将页面分配给来宾 VM) 为 CCX 的本地 DRAM (NUMA 域。

## <a name="accelerated-networking"></a>加速网络

目前未启用支持 IB 的 HPC 和 GPU Vm 上的 Azure 加速网络。 当支持此功能时，我们将通知客户。

## <a name="qp0-access-restriction"></a>qp0 访问限制

若要防止可能导致安全漏洞的低级别硬件访问，来宾 Vm 将无法访问队列对0。 这只会影响通常与 ConnectX-5 NIC 的管理相关的操作，并运行一些不受影响的诊断（如 ibdiagnet），而不是最终用户应用程序本身。

## <a name="gss-proxy"></a>GSS 代理

GSS Proxy 在 CentOS/RHEL 7.5 中有一个已知 bug，在与 NFS 一起使用时，它们可以表现为显著的性能和响应能力。 可以通过以下方式缓解这一点：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>缓存清理

在 HPC 系统上，在为下一个用户分配同一个节点之前，在作业完成之后清理内存通常非常有用。 在 Linux 中运行应用程序后，您可能会发现，当您的缓冲区内存增加时，您的可用内存会减少，而不运行任何应用程序。

![清理前的命令提示符屏幕截图](./media/known-issues/cache-cleaning-1.png)

使用 `numactl -H` 将显示哪些 NUMAnode (s) 内存通过 (可能全部) 进行缓冲。 在 Linux 中，用户可以通过三种方式清理缓存，以将缓冲或缓存的内存返回到 "免费"。 你需要是根或具有 sudo 权限。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![清理后的命令提示符屏幕截图](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>内核警告

在 Linux 下启动 HB-ACCT-WC 系列 VM 时，可能会忽略以下内核警告消息。 这是因为将在一段时间内解决的 Azure 虚拟机监控程序的已知限制。

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


## <a name="next-steps"></a>后续步骤

- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，以了解如何对工作负载进行优化配置以提高性能和可伸缩性。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 有关运行 HPC 工作负荷的更高级结构视图，请参阅 [Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。

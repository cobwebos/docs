---
title: Azure 中 Linux VM 的时间同步
description: Linux 虚拟机的时间同步。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 399022c1ef740865e4b2f7b82e2175e748a2a925
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306950"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure 中 Linux VM 的时间同步

时间同步对于安全性和事件相关性来说很重要。 有时候，它用于分布式事务实现。 多个计算机系统之间的时间准确性通过同步来实现。 同步可能受多种因素影响，包括重启以及时间源和提取时间的计算机之间的网络流量。 

Azure 受运行 Windows Server 2016 的基础设施的支持。 Windows Server 2016 已改进用于纠正时间和条件的算法，方便本地时钟与 UTC 同步。  Windows Server 2016 的准确时间功能大大改进了 VMICTimeSync 服务的控制方式，可以通过控制 VM 与主机的同步来确保时间准确。 改进包括增强 VM 启动或 VM 还原的初始时间的准确性，以及纠正中断延迟。 

> [!NOTE]
> 若要快速了解 Windows 时间服务，请参阅此[高级概述视频](https://aka.ms/WS2016TimeVideo)。
>
> 有关详细信息，请参阅 [Windows Server 2016 的准确时间](/windows-server/networking/windows-time-service/accurate-time)。 

## <a name="overview"></a>概述

计算机时钟的准确性根据计算机时钟与协调世界时 (UTC) 时间标准的接近程度来测量。 UTC 通过精确原子钟的跨国样本来定义，此类原子钟 300 年的偏差只有 1 秒。 但是，直接读取 UTC 需要专用硬件。 而时间服务器与 UTC 同步，可以从其他计算机访问，因此具备可伸缩性和可靠性。 每个计算机都有时间同步服务运行，该服务知道使用什么时间服务器，并定期检查计算机时钟是否需纠正，然后根据需要调整时间。 

Azure 主机与内部 Microsoft 时间服务器同步，这些服务器从 Microsoft 拥有的带 GPS 天线的 Stratum 1 设备获取时间。 Azure 中的虚拟机可以依赖其主机来获取准确的时间（主机时间），也可以直接从时间服务器获取时间，或者同时采用这两种方法。 

在独立硬件上，Linux OS 仅在启动时读取主机硬件时钟数据。 然后，时钟会通过 Linux 内核中的中断计时器来维护。 在此配置中，时钟会随着时间的推移而出现偏差。 在 Azure 上的较新的 Linux 发行版中，VM 可以使用 Linux Integration Services (LIS) 中随附的 VMICTimeSync 提供程序，从主机更频繁地查询时钟更新。

虚拟机与主机的交互也可能影响时钟。 在[内存保留维护](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)期间，VM 会暂停最多 30 秒的时间。 例如，在维护开始之前，VM 时钟显示上午 10:00:00，这种状态会持续 28 秒。 在 VM 恢复后，VM 上的时钟仍显示上午 10:00:00，这样就造成 28 秒的偏差。 为了进行纠正，VMICTimeSync 服务会监视主机上发生的情况，并会提示用户在 VM 上进行更改以纠正时间偏差。

如果不进行时间同步，VM 上的时钟会累积错误。 只有一个 VM 时，效果可能不明显，除非工作负荷要求极为准确的计时。 但在大多数情况下，我们有多个互连的 VM，这些 VM 使用时间来跟踪事务，因此需确保整个部署的时间一致。 当 VM 之间的时间不同时，可能会造成以下影响：

- 身份验证会失败。 安全协议（如 Kerberos）或依赖于证书的技术要求跨系统确保时间一致性。
- 如果日志（或其他数据）在时间上不一致，则很难弄清楚系统中发生了什么。 同一事件看起来就像是在不同的时间发生，难以进行关联。
- 如果时钟存在偏差，则可能造成计费不正确。



## <a name="configuration-options"></a>配置选项

一般情况下，可以通过三种方式为托管在 Azure 中的 Linux VM 配置时间同步：

- Azure 市场映像的默认配置使用 NTP 时间和 VMICTimeSync 主机时间。 
- 仅主机（使用 VMICTimeSync）。
- 在使用或不使用 VMICTimeSync 主机时间的情况下，使用另一外部时间服务器。


### <a name="use-the-default"></a>使用默认值

默认情况下，大多数适用于 Linux 的 Azure 市场映像配置为与两个源同步： 

- NTP 充当主要源，可以从 NTP 服务器获取时间。 例如，Ubuntu 16.04 LTS 市场映像使用 **ntp.ubuntu.com**。
- VMICTimeSync 服务充当次要源，用于将主机时间传递给 VM，并在 VM 因维护而暂停后进行纠正。 Azure 主机使用 Microsoft 拥有的 Stratum 1 设备来保持准确的时间。

在较新的 Linux 发行版中，VMICTimeSync 服务提供精度时间协议 (PTP) 硬件时钟源，但较早的发行版可能不提供此时钟源，因此会回退到 NTP 从主机获取时间。

若要确认 NTP 是否正确同步，请运行 `ntpq -p` 命令。

### <a name="host-only"></a>仅主机 

由于 NTP 服务器（例如 time.windows.com 和 ntp.ubuntu.com）是公共的，因此与其同步时间需要通过 Internet 发送流量。 数据包的延迟各不相同，可能会对时间同步的质量造成负面影响。通过切换到“仅主机”同步来删除 NTP 有时候可以改善时间同步结果。

如果在使用默认配置时遇到时间同步问题，则可切换到“仅主机”时间同步。 尝试“仅主机”同步，看是否会改进 VM 上的时间同步。 

### <a name="external-time-server"></a>外部时间服务器

如果有特定的时间同步要求，则可使用另一选项，即，使用外部时间服务器。 外部时间服务器可以提供特定的时间，这可以用于测试方案，确保时间在非 Microsoft 数据中心托管的计算机中的一致性，或者以特殊方式来处理闰秒问题。

可以将外部时间服务器与 VMICTimeSync 服务组合使用，提供类似于默认配置的结果。 若要处理因维护而暂停 VM 所导致的问题，最好是将外部时间服务器与 VMICTimeSync 组合使用。 

## <a name="tools-and-resources"></a>工具和资源

可以通过一些基本的命令来检查时间同步配置。 Linux 发行版的文档更详细地说明了如何才能以最佳方式为该发行版配置时间同步。

### <a name="integration-services"></a>集成服务

查看集成服务 (hv_utils) 是否已加载。

```bash
lsmod | grep hv_utils
```
看到的内容应该如下所示：

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

查看 Hyper-V 集成服务守护程序是否正在运行。

```bash
ps -ef | grep hv
```

看到的内容应该如下所示：

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>检查 PTP 时钟源

使用较新版的 Linux 时，可以在 VMICTimeSync 提供程序中获得精度时间协议 (PTP) 时钟源。 在较旧版本的 Red Hat Enterprise Linux 或 CentOS 7.x 上，可以下载 [Linux Integration Services ](https://github.com/LIS/lis-next)，并将其用于安装更新的驱动程序。 当 PTP 时钟源可用时，Linux 设备的表示形式为 /dev/ptpx。 

查看哪些 PTP 时钟源可用。

```bash
ls /sys/class/ptp
```

在此示例中，返回的值为 *ptp0*，因此我们使用它来检查时钟名称。 若要验证设备，请检查时钟名称。

```bash
cat /sys/class/ptp/ptp0/clock_name
```

此命令应返回 `hyperv`。

### <a name="chrony"></a>chrony

在 Ubuntu 19.10 及更高版本中，Red Hat Enterprise Linux 和 CentOS [chrony](https://chrony.tuxfamily.org/) 配置为使用 PTP 源时钟。 旧的 Linux 发行版使用网络时间协议守护程序 (ntpd)（不支持 PTP 源），而不是使用 chrony。 要在这些版本中启用 PTP，必须使用以下代码手动安装并配置 chrony（在 chrony.conf 中）：

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

有关 Ubuntu 和 NTP 的详细信息，请参阅[时间同步](https://ubuntu.com/server/docs/network-ntp)。

有关 Red Hat 和 NTP 的详细信息，请参阅 [配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)。 

有关 chrony 的详细信息，请参阅[使用 chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)。

如果同时启用了 chrony 和 VMICTimeSync 源，则可将一个源标记为“首选”，这样就会将另一个源设置为“备用”。 由于 NTP 服务在遇到大偏差时更新时钟需要很长时间，因此可以使用 VMICTimeSync，后者在出现 VM 暂停事件时恢复时钟的速度远远快于单独使用基于 NTP 的工具的速度。

默认情况下，chronyd 会加快或减慢系统时钟以修复任何时间偏移。 如果偏移量过大，chrony 将无法修复偏移。 若要解决此问题，可以更改 /etc/chrony.conf 中的 `makestep` 参数，以便在偏移量超过指定的阈值时强制进行时间同步。

 ```bash
makestep 1.0 -1
```

在这里，如果偏移量大于 1 秒，chrony 会强制进行时间更新。 若要应用更改，请重启 chronyd 服务：

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

在 19.10 以前的 SUSE 和 Ubuntu 版本中，使用 [systemd](https://www.freedesktop.org/wiki/Software/systemd/) 配置时间同步。 有关 Ubuntu 的详细信息，请参阅[时间同步](https://help.ubuntu.com/lts/serverguide/NTP.html)。 有关 SUSE 的详细信息，请参阅 [SUSE Linux Enterprise Server 12 SP3 发行说明](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)中的第 4.5.8 节。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Windows Server 2016 的准确时间](/windows-server/networking/windows-time-service/accurate-time)。



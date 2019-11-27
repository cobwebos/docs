---
title: Azure 中 Linux VM 的时间同步
description: Linux 虚拟机的时间同步。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 1e459e96c128e20f44f1a5adcb18c5b1824c3bf5
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534124"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure 中 Linux VM 的时间同步

时间同步对于安全性和事件关联非常重要。 有时，它用于分布式的事务实现。 可通过同步实现多个计算机系统之间的时间准确性。 同步可能受到多种因素的影响，包括重新启动以及时间源和获取时间的计算机之间的流量。 

Azure 由运行 Windows Server 2016 的基础结构提供支持。 Windows Server 2016 改进了用于校正时间的算法和本地时钟与 UTC 同步的条件。  Windows Server 2016 Accurate Time 功能还大幅改进了 VMICTimeSync 服务，该服务管理 VM 如何与主机同步以获得准确时间。 改进包括 VM 启动或 VM 还原的更精确的初始时间以及中断延迟校正。 

>[!NOTE]
>有关 Windows 时间服务的快速概述，请参阅此[高级概述视频](https://aka.ms/WS2016TimeVideo)。
>
> 有关详细信息，请参阅 [Windows Server 2016 的准确时间](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)。 

## <a name="overview"></a>概述

计算机时钟的准确度是根据计算机时钟与协调世界时 (UTC) 时间标准的接近程度来衡量的。 UTC 由多国的精确原子钟示例定义，300 年误差仅一秒。 但是，直接读取 UTC 需要专用的硬件。 相反，时间服务器与 UTC 同步，并可从其他计算机访问，以提供可伸缩性和可靠性。 每台计算机都运行时间同步服务，该服务知道要使用什么时间服务器，并定期检查计算机时钟是否需要校正以及在需要时调整时间。 

Azure 主机与内部 Microsoft 时间服务器同步，这些服务器从 Microsoft 拥有的带 GPS 天线的 Stratum 1 设备获取时间。 Azure 中的虚拟机可以依赖其主机将准确时间（主机时间）传递给 VM，也可以直接从时间服务器中获取时间，或结合使用这两种方式。 

在独立硬件上，Linux OS 仅在启动时读取主机硬件时钟。 在此之后，使用 Linux 内核中的中断计时器维护时钟。 在此配置中，时钟将随时间推移发生偏移。 在 Azure 上较新的 Linux 发行版中，VM 可以使用 Linux 集成服务 (LIS) 中包含的 VMICTimeSync 提供程序来更频繁地查询来自主机的时钟更新。

虚拟机与主机的交互也会影响时钟。 在[内存保留维护](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)期间，VM 最多暂停 30 秒。 例如，在维护开始之前，VM 时钟显示上午 10:00:00 并持续 28 秒。 VM 恢复后，VM 上的时钟仍显示上午 10:00:00，也就是差了 28 秒。 为了校正这种情况，VMICTimeSync 服务会监视主机上发生的情况，并提示在 VM 上进行更改以进行补偿。

如果没有进行时间同步，则 VM 上的时钟会累积错误。 当只有一个 VM 时，除非工作负载需要高度准确的计时，否则效果可能不会很明显。 但在大多数情况下，我们有多个相互关联的 VM，它们使用时间来跟踪事务，并且需要在整个部署中保持一致的时间。 VM 之间的时间不同时，可能会产生以下影响：

- 身份验证将会失败。 Kerberos 等安全协议或依赖于证书的技术依赖于系统间的时间一致性。
- 如果日志（或其他数据）在时间上不一致，则很难弄清楚系统中发生了什么。 同一事件看起来好像发生在不同的时间，这使关联变得困难。
- 如果时钟关闭，则可能无法正确计费。



## <a name="configuration-options"></a>配置选项

通常可通过三种方法 Azure 中托管的 Linux VM 配置时间同步：

- Azure 市场映像的默认配置同时使用 NTP 时间和 VMICTimeSync 主机时间。 
- 使用 VMICTimeSync（仅限主机）。
- 使用其他外部时间服务器，使用或不使用 VMICTimeSync 主机时间。


### <a name="use-the-default"></a>使用默认配置

默认情况下，大多数适用于 Linux 的 Azure 市场映像都配置为从两个源同步： 

- NTP 作为主服务器，从 NTP 服务器获取时间。 例如，Ubuntu 16.04 LTS 市场映像使用 ntp.ubuntu.com。
- VMICTimeSync 服务作为辅助服务器，用于将主机时间传递给 VM，并在 VM 暂停以进行维护后进行校正。 Azure 主机使用 Microsoft 拥有的 Stratum 1 设备来保持准确的时间。

在较新的 Linux 发行版中，VMICTimeSync 服务使用精确时间协议 (PTP)，但早期发行版可能不支持 PTP，将回退到 NTP 以从主机获取时间。

要确认 NTP 是否正确同步，请运行 `ntpq -p` 命令。

### <a name="host-only"></a>仅限主机 

由于诸如 time.windows.com 和 ntp.ubuntu.com 等 NTP 服务器是公共服务器，因此与它们同步时间需要通过 Internet 发送流量。 不同的数据包延迟可能会对时间同步的质量产生负面影响。通过切换到仅限主机的同步来删除 NTP 有时会缩短时间同步结果。

如果使用默认配置时遇到时间同步问题，切换到仅限主机时间同步可解决问题。 请尝试仅限主机同步，看看是否会改善 VM 上的时间同步。 

### <a name="external-time-server"></a>外部时间服务器

如果有特定的时间同步要求，还可以选择使用外部时间服务器。 外部时间服务器可以提供特定的时间，这对于测试方案非常有用，可确保与非 Microsoft 数据中心托管的计算机的时间一致性，或以特殊方式处理闰秒。

可以结合使用外部服务器与 VMICTimeSync 服务，以提供与默认配置类似的结果。 将外部时间服务器与 VMICTimeSync 相结合是处理暂停 VM 进行维护时可能引起的问题的最佳选择。 

## <a name="tools-and-resources"></a>工具和资源

可通过基本命令检查时间同步配置。 Linux 发行版文档将更详细地介绍针对发行版配置时间同步的最佳方法。

### <a name="integration-services"></a>集成服务

检查是否已加载集成服务 (hv_utils)。

```bash
lsmod | grep hv_utils
```
应可看到类似于下面的内容：

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

查看 Hyper-V 集成服务守护程序是否正在运行。

```bash
ps -ef | grep hv
```

应可看到类似于下面的内容：

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>检查 PTP

对于较新版本的 Linux，可在 VMICTimeSync 提供程序中提供精确时间协议 (PTP) 时钟源。 在较旧版本的 Red Hat Enterprise Linux 或 CentOS 7.x 上，可以下载 [Linux Integration Services ](https://github.com/LIS/lis-next)，并将其用于安装更新的驱动程序。 使用 PTP 时，Linux 设备的格式为 /dev/ptp *x*。 

查看可用的 PTP 时钟源。

```bash
ls /sys/class/ptp
```

在此示例中，返回的值为 ptp0，因此我们使用它来检查时钟名称。 要验证设备，请检查时钟名称。

```bash
cat /sys/class/ptp/ptp0/clock_name
```

这应返回 hyperv。

### <a name="chrony"></a>chrony

在 Red Hat Enterprise Linux 和 CentOS 7.x 上，[chrony](https://chrony.tuxfamily.org/) 配置为使用 PTP 源时钟。 网络时间协议守护程序 (ntpd) 不支持 PTP 源，因此建议使用 chronyd。 要启用 PTP，请更新 chrony.conf。

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

有关 Red Hat 和 NTP 的详细信息，请参阅[配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp)。 

有关 chrony 的详细信息，请参阅[使用 chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony)。

如果同时启用了 chrony 和 TimeSync 源，则可以将其中一个源标记为“首选”，将另一个源设置为备份。 由于 NTP 服务只会在很长一段时间后才会更新偏差较大的时钟，因此与仅基于 NTP 的工具相比，VMICTimeSync 可更快地从暂停的 VM 事件中恢复时钟。

默认情况下，chronyd 加速或减速系统时钟，以解决任何时间偏移。 如果偏移过大，chrony 将无法修复偏移。 若要解决此情况，可以更改 **/etc/chrony.conf**中的 `makestep` 参数，以在偏移超出指定的阈值时强制 timesync。
 ```bash
makestep 1.0 -1
```
如果偏移大于1秒，chrony 将强制进行时间更新。 若要应用更改，请重新启动 chronyd 服务。

```bash
systemctl restart chronyd
```


### <a name="systemd"></a>systemd 

在 Ubuntu 和 SUSE 上，使用 [systemd](https://www.freedesktop.org/wiki/Software/systemd/) 配置时间同步。 有关 Ubuntu 的详细信息，请参阅[时间同步](https://help.ubuntu.com/lts/serverguide/NTP.html)。 有关 SUSE 的详细信息，请参阅 [SUSE Linux Enterprise Server 12 SP3 发行说明](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)中的第 4.5.8 节。



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Windows Server 2016 的准确时间](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)。



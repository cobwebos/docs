---
title: Azure 中 Windows VM 的时间同步 | Microsoft Docs
description: Windows 虚拟机的时间同步。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: d7363c3d1cd3aaf6aae8cadbea232c909000f214
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669596"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Azure 中 Windows VM 的时间同步

时间同步对于安全性和事件关联非常重要。 有时，它用于分布式的事务实现。 可通过同步实现多个计算机系统之间的时间准确性。 同步可能受到多种因素的影响，包括重新启动以及时间源和获取时间的计算机之间的流量。 

Azure 现在由运行 Windows Server 2016 的基础结构提供支持。 Windows Server 2016 改进了用于校正时间的算法和本地时钟与 UTC 同步的条件。  Windows Server 2016 还改进了 VMICTimeSync 服务，该服务管理 VM 如何与主机同步以获得准确时间。 改进包括 VM 启动或 VM 恢复的更精确的初始时间以及提供给 Windows 时间 (W32time) 的示例的中断延迟校正。 


>[!NOTE]
>有关 Windows 时间服务的快速概述，请参阅此[高级概述视频](https://aka.ms/WS2016TimeVideo)。
>
> 有关详细信息，请参阅 [Windows Server 2016 的准确时间](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)。 

## <a name="overview"></a>概述

计算机时钟的准确度是根据计算机时钟与协调世界时 (UTC) 时间标准的接近程度来衡量的。 UTC 由多国的精确原子钟示例定义，300 年误差仅一秒。 但是，直接读取 UTC 需要专用的硬件。 相反，时间服务器与 UTC 同步，并可从其他计算机访问，以提供可伸缩性和可靠性。 每台计算机都运行时间同步服务，该服务知道要使用什么时间服务器，并定期检查计算机时钟是否需要校正以及在需要时调整时间。 

Azure 主机与内部 Microsoft 时间服务器同步，这些服务器从 Microsoft 拥有的带 GPS 天线的 Stratum 1 设备获取时间。 Azure 中的虚拟机可以依赖其主机将准确时间（主机时间）传递给 VM，也可以直接从时间服务器中获取时间，或结合使用这两种方式。 

虚拟机与主机的交互也会影响时钟。 在[内存保留维护](maintenance-and-updates.md#memory-preserving-maintenance)期间，VM 最多暂停 30 秒。 例如，在维护开始之前，VM 时钟显示上午 10:00:00 并持续 28 秒。 VM 恢复后，VM 上的时钟仍显示上午 10:00:00，也就是差了 28 秒。 为了校正这种情况，VMICTimeSync 服务会监视主机上发生的情况，并提示在 VM 上进行更改以进行补偿。

VMICTimeSync 服务以采样或同步模式运行，只会影响时钟前进。 在需要运行 W32time 的采样模式下，VMICTimeSync 服务每 5 秒轮询主机一次并向 W32time 提供时间样本。 W32time 服务大约每隔 30 秒就会抽取一次最新的时间样本并使用它来影响来宾的时钟。 如果来宾已被恢复，或者来宾的时钟比主机时钟慢 5 秒以上，则将激活同步模式。 在 W32time 服务正常运行的情况下，后一种情况应永远不会发生。

如果没有进行时间同步，则 VM 上的时钟会累积错误。 当只有一个 VM 时，除非工作负载需要高度准确的计时，否则效果可能不会很明显。 但在大多数情况下，我们有多个相互关联的 VM，它们使用时间来跟踪事务，并且需要在整个部署中保持一致的时间。 VM 之间的时间不同时，可能会产生以下影响：

- 身份验证将会失败。 Kerberos 等安全协议或依赖于证书的技术依赖于系统间的时间一致性。 
- 如果日志（或其他数据）在时间上不一致，则很难弄清楚系统中发生了什么。 同一事件看起来好像发生在不同的时间，这使关联变得困难。
- 如果时钟关闭，则可能无法正确计费。

通过将 Windows Server 2016 用作来宾操作系统，可以实现 Windows 部署的最佳结果，从而确保可以使用时间同步的最新改进。

## <a name="configuration-options"></a>配置选项

为 Azure 中托管的 Windows VM 配置时间同步有三个选项：

- 主机时间和 time.windows.com。 这是 Azure 市场映像中使用的默认配置。
- 仅限主机。
- 使用其他外部时间服务器，无论是否使用主机时间。


### <a name="use-the-default"></a>使用默认配置

默认情况下，Windows OS VM 映像配置为 w32time，从两个源进行同步： 

- NtpClient 提供程序从 time.windows.com 获取信息。
- VMICTimeSync 服务用于将主机时间传递给 VM，并在 VM 暂停以进行维护后进行更正。 Azure 主机使用 Microsoft 拥有的 Stratum 1 设备来保持准确的时间。

w32time 将按以下优先级顺序选择时间提供程序：层级、根延迟、根分散、时间偏移。 在大多数情况下，w32time 首选 time.windows.com 而非主机，因为 time.windows.com 会报告较低的层次。 

对于域加入的计算机，域本身会建立时间同步层次结构，但是林根仍然需要从某个地方获取时间，并且以下注意事项仍然适用。


### <a name="host-only"></a>仅限主机 

由于 time.windows.com 是公共 NTP 服务器，因此与其同步时间需要通过 Internet 发送流量，不同的数据包延迟会对时间同步的质量产生负面影响。通过切换到仅限主机同步来删除 time.windows.com 有时可以改善时间同步结果。

如果使用默认配置时遇到时间同步问题，切换到仅限主机时间同步可解决问题。 请尝试仅限主机同步，看看是否会改善 VM 上的时间同步。 

### <a name="external-time-server"></a>外部时间服务器

如果有特定的时间同步要求，还可以选择使用外部时间服务器。 外部时间服务器可以提供特定的时间，这对于测试方案非常有用，可确保与非 Microsoft 数据中心托管的计算机的时间一致性，或以特殊方式处理闰秒。

可以结合使用外部服务器与 VMICTimeSync 服务和 VMICTimeProvider，以提供与默认配置类似的结果。 

## <a name="check-your-configuration"></a>检查配置


检查 NtpClient 时间提供程序是否配置为使用显式 NTP 服务器 (NTP) 或域时间同步 (NT5DS)。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

如果 VM 正在使用 NTP，则将看到以下输出：

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

要查看 NtpClient 时间提供程序使用的时间服务器，请在提升的命令提示符下键入：

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

如果 VM 正在使用默认设置，则输出将如下所示：

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


要查看当前正在使用的时间提供程序。

```
w32tm /query /source
```


以下是可以看到的输出及其含义：
    
- time.windows.com - 在默认配置中，w32time 会从 time.windows.com 获取时间。 时间同步质量取决于与其的 Internet 连接，并受数据包延迟的影响。 这是默认设置的常规输出。
- VM IC 时间同步提供程序 - VM 正在与主机同步时间。 如果选择加入仅主机时间同步或者 NtpServer 此时不可用，通常会出现这种情况。 
- 域服务器 - 当前计算机位于域中，域定义时间同步层次结构。
- 其他一些服务器 - 将 w32time 明确配置为从其他服务器获取时间。 时间同步质量取决于此时间服务器质量。
- 本地 CMOS 时钟 - 时钟未同步。 如果 w32time 在重新启动后没有足够的时间启动，或者所有配置的时间源都不可用时，则可以获得此输出。


## <a name="opt-in-for-host-only-time-sync"></a>选择加入仅限主机的时间同步

Azure 不断致力于改善主机上的时间同步，并且可以保证所有时间同步基础结构都在 Microsoft 拥有的数据中心中并置。 如果对使用 time.windows.com 作为主要时间源的默认设置有时间同步问题，则可以使用以下命令选择加入仅限主机的时间同步。

将 VMIC 提供程序标记为已启用。 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

将 NTPClient 提供程序标记为已禁用。

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

重启 w32time 服务。

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 和 R2 VM 

Windows Server 2012 和 Windows Server 2012 R2 具有不同的时间同步默认设置。默认情况下，w32time 的配置方式更倾向于服务的低开销而非精确的时间。 

如果要将 Windows Server 2012 和 2012 R2 部署移动到使用更倾向于精确时间的较新默认设置，则可以应用以下设置。

更新 w32time 轮询并更新间隔，以匹配 Windows Server 2016 设置。

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

为了使 w32time 能够使用新的轮询间隔，请将 NtpServers 标记为使用它们。 如果服务器使用 0x1 位标记掩码进行注释，则会覆盖此机制，并且 w32time 将改用 SpecialPollInterval。 确保指定的 NTP 服务器使用 0x8 标志或根本不使用标志：

检查所使用的 NTP 服务器正在使用的标志。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>后续步骤

以下是有关时间同步的更多详细信息的链接：

- [Windows 时间服务工具和设置](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Windows Server 2016 的改进](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Windows Server 2016 的准确时间](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [为高精度环境配置 Windows 时间服务的支持边界](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)



---
title: Azure 中 Windows VM 的时间同步
description: Windows 虚拟机的时间同步。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 830bdd45be4b0365ac45bc3ea366b99a34882a4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871473"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Azure 中 Windows VM 的时间同步

时间同步对于安全性和事件相关性来说很重要。 有时候，它用于分布式事务实现。 多个计算机系统之间的时间准确性通过同步来实现。 同步可能受多种因素影响，包括重启以及时间源和提取时间的计算机之间的网络流量。 

Azure 现在受运行 Windows Server 2016 的基础设施的支持。 Windows Server 2016 已改进用于纠正时间和条件的算法，方便本地时钟与 UTC 同步。  Windows Server 2016 还改进了 VMICTimeSync 服务，该服务用于控制 VM 与主机的同步方式，以确保时间准确。 改进包括增强 VM 启动或 VM 还原的初始时间的准确性，以及纠正提供给 Windows 时间 (W32time) 服务的示例的中断延迟。 


>[!NOTE]
>若要快速了解 Windows 时间服务，请参阅此[高级概述视频](https://aka.ms/WS2016TimeVideo)。
>
> 有关详细信息，请参阅 [Windows Server 2016 的准确时间](/windows-server/networking/windows-time-service/accurate-time)。 

## <a name="overview"></a>概述

计算机时钟的准确性根据计算机时钟与协调世界时 (UTC) 时间标准的接近程度来测量。 UTC 通过精确原子钟的跨国样本来定义，此类原子钟 300 年的偏差只有 1 秒。 但是，直接读取 UTC 需要专用硬件。 而时间服务器与 UTC 同步，可以从其他计算机访问，因此具备可伸缩性和可靠性。 每个计算机都有时间同步服务运行，该服务知道使用什么时间服务器，并定期检查计算机时钟是否需纠正，然后根据需要调整时间。 

Azure 主机与内部 Microsoft 时间服务器同步，这些服务器从 Microsoft 拥有的带 GPS 天线的 Stratum 1 设备获取时间。 Azure 中的虚拟机可以依赖其主机来获取准确的时间（主机时间），也可以直接从时间服务器获取时间，或者同时采用这两种方法。 

虚拟机与主机的交互也可能影响时钟。 在[内存保留维护](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)期间，VM 会暂停最多 30 秒的时间。 例如，在维护开始之前，VM 时钟显示上午 10:00:00，这种状态会持续 28 秒。 在 VM 恢复后，VM 上的时钟仍显示上午 10:00:00，这样就造成 28 秒的偏差。 为了进行纠正，VMICTimeSync 服务会监视主机上发生的情况，并会提示用户在 VM 上进行更改以纠正时间偏差。

VMICTimeSync 服务以采样或同步模式运行，只会影响时钟前进。 在需要运行 W32time 的采样模式下，VMICTimeSync 服务每 5 秒轮询主机一次并向 W32time 提供时间样本。 W32time 服务大约每隔 30 秒就会抽取一次最新的时间样本并使用它来影响来宾的时钟。 如果来宾已被恢复，或者来宾的时钟比主机时钟慢 5 秒以上，则将激活同步模式。 在 W32time 服务正常运行的情况下，后一种情况应永远不会发生。

如果不进行时间同步，VM 上的时钟会累积错误。 只有一个 VM 时，效果可能不明显，除非工作负荷要求极为准确的计时。 但在大多数情况下，我们有多个互连的 VM，这些 VM 使用时间来跟踪事务，因此需确保整个部署的时间一致。 当 VM 之间的时间不同时，可能会造成以下影响：

- 身份验证会失败。 安全协议（如 Kerberos）或依赖于证书的技术要求跨系统确保时间一致性。 
- 如果日志（或其他数据）在时间上不一致，则很难弄清楚系统中发生了什么。 同一事件看起来就像是在不同的时间发生，难以进行关联。
- 如果时钟存在偏差，则可能造成计费不正确。

可以将 Windows Server 2016 用作来宾操作系统，这样可确保使用时间同步方面的最新改进，获得 Windows 部署的最佳结果。

## <a name="configuration-options"></a>配置选项

可以通过三个选项来配置托管在 Azure 中的 Windows VM 的时间同步：

- 主机时间和 time.windows.com。 这是在 Azure 市场映像中使用的默认配置。
- 仅主机。
- 在使用或不使用主机时间的情况下，使用另一外部时间服务器。


### <a name="use-the-default"></a>使用默认值

默认情况下，Windows OS VM 映像配置为允许 w32time 与两个源同步： 

- NtpClient 提供程序，从 time.windows.com 获取信息。
- VMICTimeSync 服务，用于将主机时间传递给 VM，并在 VM 因维护而暂停后进行纠正。 Azure 主机使用 Microsoft 拥有的 Stratum 1 设备来保持准确的时间。

w32time 会按以下优先级顺序来首选时间提供程序：层次级别、根延迟、根分散、时间偏差。 在大多数情况下，Azure VM 上的 w32time 会首选主机时间，因为它会进行评估以比较两个时间源。 

对于已加入域的计算机来说，域本身已建立时间同步层次结构，但林根仍需从某个位置获取时间，因此仍需考虑以下注意事项。


### <a name="host-only"></a>仅主机 

由于 time.windows.com 是公共 NTP 服务器，与其同步时间需要通过 Internet 发送流量，而数据包的延迟各不相同，因此可能会对时间同步的质量造成负面影响。通过切换到“仅主机”同步来删除 time.windows.com 有时候可以改善时间同步结果。

如果在使用默认配置时遇到时间同步问题，则可切换到“仅主机”时间同步。 尝试“仅主机”同步，看是否会改进 VM 上的时间同步。 

### <a name="external-time-server"></a>外部时间服务器

如果有特定的时间同步要求，则可使用另一选项，即，使用外部时间服务器。 外部时间服务器可以提供特定的时间，这可以用于测试方案，确保时间在非 Microsoft 数据中心托管的计算机中的一致性，或者以特殊方式来处理闰秒问题。

可以将外部服务器与 VMICTimeSync 服务和 VMICTimeProvider 组合使用，提供类似于默认配置的结果。 

## <a name="check-your-configuration"></a>检查配置


检查是否已将 NtpClient 时间提供程序配置为使用显式 NTP 服务器 (NTP) 或域时间同步 (NT5DS)。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

如果 VM 使用 NTP，则会看到以下输出：

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

若要查看 NtpClient 时间提供程序正在使用什么时间服务器，请在权限提升的命令提示符处键入以下命令：

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

如果 VM 使用的是默认值，则输出将如下所示：

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


若要查看当前正使用什么时间提供程序，请键入以下命令：

```
w32tm /query /source
```


下面是可能会看到的输出及其含义：
    
- **time.windows.com** - 在默认配置中，w32time 会从 time.windows.com 获取时间。 时间同步质量取决于到它的 Internet 连接，受数据包延迟的影响。 这是你将在物理计算机上获得的常见输出。
- **VM IC 时间同步提供程序**  -vm 正在从主机同步时间。 这是你将在 Azure 中运行的虚拟机上获得的常见输出。 
- 你的域服务器 - 当前计算机位于某个域中，该域定义时间同步层次结构。
- 某个其他的服务器 - w32time 已显式配置为从该服务器获取时间。 时间同步质量取决于该时间服务器质量。
- **本地 CMOS 时钟** - 时钟未同步。 如果 w32time 在重启后还没有足够的时间启动，或者所有配置的时间源均不可用，则可能获得此输出。


## <a name="opt-in-for-host-only-time-sync"></a>选择启用“仅主机”时间同步

Azure 不断致力于改善主机上的时间同步，并且可以保证所有时间同步基础结构都在 Microsoft 拥有的数据中心中并置。 如果首选使用 time.windows.com 作为主时间源的默认设置有时间同步问题，可通过以下命令来选择启用“仅主机”时间同步。

将 VMIC 提供程序标记为“已启用”。 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

将 NTPClient 提供程序标记为“已禁用”。

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

重启 w32time 服务。

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 和 R2 VM 

Windows Server 2012 和 Windows Server 2012 R2 对于时间同步有不同的默认设置。w32time 默认配置为首选降低服务的开销而不是提高其时间准确度。 

若要移动 Windows Server 2012 和 2012 R2 部署，以便使用首选提高时间准确度的较新默认设置，则可应用以下设置。

请更新 w32time 轮询和更新时间间隔，使之与 Windows Server 2016 设置匹配。

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

为了让 w32time 能够使用新的轮询时间间隔，需将 NtpServers 标记为使用它们。 如果使用 0x1 位标志掩码对服务器进行批注，则会替代此机制，w32time 会改用 SpecialPollInterval。 请确保指定的 NTP 服务器使用 0x8 标志或根本不使用任何标志：

检查哪些标志正用于已使用的 NTP 服务器。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>后续步骤

下面是有关时间同步的更多详细信息的链接：

- [Windows 时间服务工具和设置](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Windows Server 2016 改进 ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Windows Server 2016 的准确时间](/windows-server/networking/windows-time-service/accurate-time)
- [Support boundary to configure the Windows Time service for high-accuracy environments](/windows-server/networking/windows-time-service/support-boundary)（为高准确性环境配置 Windows 时间服务所需的支持边界）

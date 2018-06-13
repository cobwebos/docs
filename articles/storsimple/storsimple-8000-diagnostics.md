---
title: 用于排查 StorSimple 8000 设备问题的诊断工具 | Microsoft Docs
description: 介绍 StorSimple 设备模式，并说明如何使用 Windows PowerShell for StorSimple 更改设备模式。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7199009553eb7aae31db3f913fe4de87e03d74ba
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
ms.locfileid: "27744106"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>使用 StorSimple 诊断工具排查 8000 系列设备的问题

## <a name="overview"></a>概述

StorSimple 诊断工具可诊断 StorSimple 设备的系统、性能、网络和硬件组件运行状况相关的问题。 可在各种场合中使用该诊断工具。 这些场合包括工作负荷规划、部署 StorSimple 设备、评估网络环境，以及确定运行中设备的性能。 本文概述该诊断工具，并介绍如何在 StorSimple 设备上使用该工具。

该诊断工具主要适用于 StorSimple 8000 系列的本地设备（8100 和 8600）。

## <a name="run-diagnostics-tool"></a>运行诊断工具

可通过 StorSimple 设备的 Windows PowerShell 界面运行此工具。 可使用以下两种方法访问设备的本地接口：

* [使用 PuTTY 连接到设备串行控制台](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)。
* [通过 Windows PowerShell for StorSimple 远程访问该工具](storsimple-8000-remote-connect.md)。

本文假设已通过 PuTTY 连接到设备串行控制台。

#### <a name="to-run-the-diagnostics-tool"></a>运行诊断工具

连接到设备的 Windows PowerShell 界面后，请执行以下步骤来运行 cmdlet。
1. 按照[使用 PuTTY 连接到设备串行控制台](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)中的步骤登录到设备串行控制台。

2. 输入以下命令：

    `Invoke-HcsDiagnostics`

    如果未指定 scope 参数，cmdlet 将执行所有诊断测试。 这些测试包括系统、硬件组件运行状况、网络和性能。 
    
    若要运行特定的测试，请指定 scope 参数。 例如，如果只想运行网络测试，请键入

    `Invoke-HcsDiagnostics -Scope Network`

3. 选择 PuTTY 窗口中的输出，并将其复制到某个文本文件做进一步分析。

## <a name="scenarios-to-use-the-diagnostics-tool"></a>诊断工具的使用场合

使用诊断工具可以排查系统的网络、性能、系统和硬件运行状况问题。 下面是一些可能的使用场合：

* **设备脱机** - StorSimple 8000 系列设备脱机。 但是，在 Windows PowerShell 界面中，两个控制器似乎已启动并正在运行。
    * 在这种情况下，可以使用此工具来确定网络状态。
         
         > [!NOTE]
         > 注册（或者通过设置向导配置）之前，请不要使用此工具来评估设备上的性能和网络设置。 在运行设置向导和注册期间，系统会将一个有效的 IP 分配给设备。 可在未注册的设备上运行此 cmdlet，以诊断硬件运行状况和系统。 使用 scope 参数，例如：
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **持续性的设备问题** - 遇到了似乎一直存在的设备问题。 例如，注册一直失败。 设备成功注册并运行一段时间后，也可能会遇到设备问题。
    * 在这种情况下，可以先使用此工具进行初步故障排除，然后向 Microsoft 支持部门提出服务请求。 我们建议运行此工具并捕获其输出。 然后，可将此输出信息提供给支持部门，加快故障排除的速度。
    * 如果发生任何硬件组件或群集故障，请提出支持请求。

* **设备性能较低** - StorSimple 设备运行速度缓慢。
    * 在这种情况下，请结合设置为 performance 的 scope 参数运行此 cmdlet。 分析输出。 获取云读写延迟。 使用报告的延迟作为最高可实现目标，考虑到内部数据处理的某些开销，并在系统上部署工作负荷。 有关详细信息，请参阅 [Use the network test to troubleshoot device performance](#network-test)（使用网络测试排查设备性能问题）。


## <a name="diagnostics-test-and-sample-outputs"></a>诊断测试和示例输出

### <a name="hardware-test"></a>硬件测试

此测试确定系统上运行的硬件组件、USM 固件和磁盘固件的状态。

* 报告的硬件组件包括未通过测试或者系统中不存在的组件。
* 将报告系统中控制器 0、控制器 1 和共享组件的 USM 固件与磁盘固件版本。 有关硬件组件的完整列表，请参阅：

    * [Components in primary enclosure](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)（主机箱中的组件）
    * [Components in EBOD enclosure](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)（EBOD 机箱中的组件）

> [!NOTE]
> 如果硬件测试报告了组件故障，请[向 Microsoft 支持部门提出服务请求](storsimple-8000-contact-microsoft-support.md)。

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>在 8100 设备上运行的硬件测试的示例输出

下面是在 StorSimple 8100 设备上运行该测试后的示例输出。 8100 型设备没有 EBOD 机箱。 因此，不会报告 EBOD 控制器组件。

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>系统测试

此测试报告设备的系统信息、可用更新、群集信息和服务信息。

* 系统信息包括型号、设备序列号、时区、控制器状态，以及系统上运行的详细软件版本。 若要了解以输出形式报告的各个系统参数，请参阅[解释系统信息](#appendix-interpreting-system-information)。

* 更新可用性报告是否有可用的例行模式和维护模式，以及相关的包名称。 如果 `RegularUpdates` 和 `MaintenanceModeUpdates` 为 `false`，则表示没有可用的更新， 设备使用的是最新版本。
* 群集信息包含所有 HCS 群集组的各个逻辑组件的相关信息及其各自的状态。 如果在此报告部分中看到脱机的群集组，请[与 Microsoft 支持部门联系](storsimple-8000-contact-microsoft-support.md)。
* 服务信息包含设备上运行的所有 HCS 和 CiS 服务的名称和状态。 此信息可帮助 Microsoft 支持部门排查设备问题。

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>在 8100 设备上运行的系统测试的示例输出

下面是在 8100 设备上运行的系统测试的示例输出。

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_servic         Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>网络测试

此项测试验证 StorSimple 设备上的网络接口、端口、DNS 和 NTP 服务器连接、SSL 证书、存储帐户凭据、与更新服务器的连接以及 Web 代理连接的状态。

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>仅启用 DATA0 时的网络测试示例输出

下面是在 8100 设备上运行该测试后的示例输出。 在输出中可以看到：
* 只启用并配置了 DATA 0 和 DATA 1 网络接口。
* 未在门户中启用 DATA 2 - 5。
* DNS 服务器配置有效，设备可通过 DNS 服务器建立连接。
* NTP 服务器连接也正常。
* 已打开端口 80 和 443。 但是，端口 9354 被阻止。 根据[系统网络要求](storsimple-system-requirements.md)，需要打开此端口才能进行服务总线通信。
* SSL 证书有效。
* 设备可连接到存储帐户：_myss8000storageacct_。
* 与更新服务器的连接有效。
* 未在此设备上配置 Web 代理。

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>启用 DATA0 和 DATA1 时的网络测试示例输出

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>性能测试

此项测试报告设备的云性能，反映云读写延迟。 可以使用此工具来建立可通过 StorSimple 实现的云性能基准。 该工具将报告连接可实现的最高性能（读写延迟的最佳方案）。

由于该工具将报告可实现的最高性能，因此，在部署工作负荷时，我们可将报告的读写延迟用作目标。

该项测试将模拟与设备上不同卷类型关联的 Blob 大小。 普通的分层卷以及本地固定卷的备份使用 64 KB Blob 大小。 已选中存档选项的分层卷使用 512 KB Blob 数据大小。 如果设备中配置了分层卷和本地固定卷，则只会运行对应于 64 KB Blob 数据大小的测试。

若要使用此工具，请执行以下步骤：

1.  首先，同时创建分层卷以及选中存档选项的分层卷。 此操作可确保该工具同时针对 64 KB 和 512 KB Blob 大小运行测试。

2. 创建并配置卷后，请运行 cmdlet。 键入：

    `Invoke-HcsDiagnostics -Scope Performance`

3. 记下工具报告的读写延迟。 此项测试可能需要运行几分钟，并报告结果。

4. 如果连接延迟全部低于预期的范围，则在部署工作负荷时，可将工具报告的延迟用作最高可实现目标。 考虑到内部数据处理的某些开销。

    如果诊断工具报告的读写延迟较高：

    1. 为 Blob 服务配置存储分析，并分析输出以了解 Azure 存储帐户的延迟。 有关详细说明，请参阅 [enable and configure Storage Analytics](../storage/common/storage-enable-and-view-metrics.md)（启用和配置存储分析）。 如果这些延迟也是很高，与 StorSimple 诊断工具提供的数字相当，则需要提出 Azure 存储相关的服务请求。

    2. 如果存储帐户延迟较低，请联系网络管理员调查网络中的任何延迟问题。

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>在 8100 设备上运行的性能测试的示例输出

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>附录：解释系统信息

下表描述了系统信息中 Windows PowerShell 参数的含义。 

| PowerShell 参数    | 说明  |
|-------------------------|------------------|
| 实例 ID             | 每个控制器都有一个关联的唯一标识符或 GUID。|
| 名称​​                    | 在部署设备期间通过 Azure 门户配置的设备友好名称。 默认的友好名称为设备序列号。 |
| 模型                   | StorSimple 8000 系列设备的型号。 型号可能是 8100 或 8600。|
| 序列号            | 设备序列号是出厂分配的，长度为 15 个字符。 例如，8600-SHX0991003G44HT 表示：<br> 8600 – 设备型号。<br>SHX – 制造地。<br> 0991003 – 具体的产品。 <br> G44HT – 最后 5 位是递增的，确保构成唯一的序列号。 这组编号不一定是按顺序递增的。|
| TimeZone                | 部署设备期间在 Azure 门户中配置的设备时区。|
| CurrentController       | 通过 StorSimple 设备的 Windows PowerShell 界面连接到的控制器。|
| ActiveController        | 在设备上处于活动状态、控制所有网络和磁盘操作的控制器。 这可能是控制器 0 或控制器 1。  |
| Controller0Status       | 设备上控制器 0 的状态。 控制器状态可能为正常、处于恢复模式或不可访问。|
| Controller1Status       | 设备上控制器 1 的状态。  控制器状态可能为正常、处于恢复模式或不可访问。|
| SystemMode              | StorSimple 设备的总体状态。 设备状态可能是正常、维护或已解除（对应于 Azure 门户中的“已停用”）。|
| FriendlySoftwareVersion | 对应于设备软件版本的友好字符串。 对于运行 Update 4 的系统，友好的软件版本为 StorSimple 8000 Series Update 4.0。|
| HcsSoftwareVersion      | 设备上运行的 HCS 软件版本。 例如，对应于 StorSimple 8000 Series Update 4.0 的 HCS 软件版本为 6.3.9600.17820。 |
| ApiVersion              | HCS 设备的 Windows PowerShell API 软件版本。|
| VhdVersion              | 设备随附的出厂映像软件版本。 如果将设备重置为出厂默认设置，则设备将运行此软件版本。|
| OSVersion               | 设备上运行的 Windows Server 操作系统软件版本。 StorSimple 设备基于对应于 6.3.9600 的 Windows Server 2012 R2。|
| CisAgentVersion         | StorSimple 设备上运行的 Cis 代理版本。 此代理可帮助与 Azure 中运行的 StorSimple Manager 服务通信。|
| MdsAgentVersion         | 对应于 StorSimple 设备上运行的 Mds 代理的版本。 此代理可将数据移到监视和诊断服务 (MDS)。|
| Lsisas2Version          | 对应于 StorSimple 设备上的 LSI 驱动程序的版本。|
| Capacity                | 设备的总容量，以字节为单位。|
| RemoteManagementMode    | 指示是否可以通过设备的 Windows PowerShell 界面远程管理该设备。 |
| FipsMode                | 指示是否在设备上启用了美国联邦信息处理标准 (FIPS) 模式。 FIPS 140 标准定义的加密算法已经过批准，可以用于美国联邦政府计算机系统来保护敏感数据。 对于运行 Update 4 或更高版本的设备，FIPS 模式默认已启用。 |

## <a name="next-steps"></a>后续步骤

* 了解 [Invoke-HcsDiagnostics cmdlet 的语法](https://technet.microsoft.com/library/mt795371.aspx)。

* 了解有关如何在 StorSimple 设备上[排查部署问题](storsimple-troubleshoot-deployment.md)。

---
title: 如何在 Microsoft Azure 中使用 PerfInsights Linux |Microsoft Docs
description: 了解如何使用 PerfInsights 来排查 Linux VM 性能问题。
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: f618066f19a5cbbf25bc1fcc872cc654ce96dae3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857217"
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload)是一个自助诊断工具, 可用于收集和分析诊断数据, 并提供一个报表来帮助排查 Azure 中的 Linux 虚拟机性能问题。 PerfInsights 可以作为独立的工具在支持的虚拟机上运行, 也可以使用[Azure 虚拟机的性能诊断](performance-diagnostics.md)直接从门户运行。

如果遇到虚拟机性能问题，我们建议在联系支持人员之前先运行此工具。

## <a name="supported-troubleshooting-scenarios"></a>支持的故障排除方案

PerfInsights 可以收集和分析多种信息。 以下部分介绍了常见方案。

### <a name="quick-performance-analysis"></a>快速性能分析

此方案收集基本信息, 如虚拟机的存储和硬件配置、各种日志, 包括:

- 操作系统信息

- PCI 设备信息

- 常规来宾操作系统日志

- 配置文件

- 存储信息

- Azure 虚拟机配置 (使用[Azure 实例元数据服务](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)收集)

- 正在运行的进程的列表、磁盘、内存和 CPU 使用率

- 网络信息

这属于被动收集信息，不会影响系统。

>[!Note]
>"快速性能分析" 方案将自动包含在以下每个方案中:

### <a name="performance-analysis"></a>性能分析

此方案类似于快速性能分析, 但允许捕获更长时间的诊断信息。

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights 收集的信息类型

收集有关 Linux 虚拟机、操作系统、块设备、高资源使用者、配置和各种日志的信息。 以下是更多详细信息:

- 操作系统
  - Linux 分发版和版本
  - 内核信息
  - 驱动程序信息

- 硬件
  - PCI 设备 [`*`]

- 进程和内存
  - 进程列表 (任务名称、使用的内存、打开的文件)
  - 总计、可用和可用的物理内存
  - 总计、可用和可用的交换内存
  - 性能分析捕获 CPU 和进程 CPU 使用率 (以5秒为间隔)
  - 在5秒的时间间隔内分析进程内存使用率

- 网络  
  - 包含适配器统计信息的网络适配器列表
  - 网络路由表
  - 打开的端口和状态

- 存储
  - 阻止设备列表
  - 分区列表
  - 装入点列表
  - MDADM 卷信息
  - LVM 卷信息
  - 在所有磁盘上按5秒的时间间隔捕获事件

- 日志
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[extension 文件夹]/\*日志\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - 过去五天 journalctl 的输出

- [Azure 虚拟机实例元数据](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] PCI 信息尚未收集在 Debian 和 SLES 分发版上

## <a name="run-the-perfinsights-linux-on-your-vm"></a>在 VM 上运行 PerfInsights Linux

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>在运行该工具之前, 必须知道哪些内容

#### <a name="tool-requirements"></a>工具要求

- 此工具必须在有性能问题的 VM 上运行。
- 必须在 VM 上安装 Python 2。7

- 目前支持以下分发版:

    | 分发组               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux 服务器        | 6.10 [`*`], 7.3, 7.6, 7.5 (Oracle-数据库-Ee 13.8 marketplace 映像)|
    | CentOS                     | 6.5 [`*`], 7。6                                    |
    | RHEL                       | 7.2、7.5、8.0 [`*`]                               |
    | Ubuntu                     | 14.04、16.04、18.04                               |
    | Debian                     | 8、9、10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] 请参阅[已知问题](#known-issues)部分

### <a name="known-issues"></a>已知问题

- RHEL 8 默认情况下未安装 Python。 若要运行 PerfInsights Linux, 必须首先安装 Python 2。7

- CentOS 1.x 上的来宾代理信息收集可能会失败

- 基于 Debian 的分发中不收集 PCI 设备信息

- 在某些分发中部分收集 LVM 信息

### <a name="how-do-i-run-perfinsights"></a>如何实现运行 PerfInsights

可以通过 Azure 门户安装 Azure 性能诊断, 在虚拟机上运行 PerfInsights。 也可以将其作为独立工具运行。

>[!Note]
>PerfInsights 只收集和分析数据。 它不会对系统做出任何修改。

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>从 Azure 门户安装并运行 PerfInsights

有关此选项的详细信息, 请参阅[Azure 性能诊断](performance-diagnostics.md)。  

#### <a name="run-perfinsights-in-standalone-mode"></a>在独立模式下运行 PerfInsights

要运行 PerfInsights 工具，请按照以下步骤操作：

1. 将[PerfInsights](https://aka.ms/perfinsightslinuxdownload)下载到虚拟机上的某个文件夹中, 并使用终端中的以下命令提取内容。

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. 导航到包含`perfinsights.py`文件的文件夹, 然后运行`perfinsights.py`以查看可用的命令行参数。

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights Linux 命令行输出的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    运行 PerfInsights 方案的基本语法是：

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    你可以使用以下示例运行快速性能分析方案1分钟, 并在/tmp/output 文件夹下创建结果:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    你可以使用以下示例运行性能分析方案5分钟, 并将结果 tar 球上传到存储帐户:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >运行方案之前，PerfInsights 会提示用户同意共享诊断信息并同意 EULA。 使用 **-a 或--accept------------------------**
    >
    >如果你具有与 Microsoft 的活动支持票证, 并按你使用的支持工程师的请求运行 PerfInsights, 请确保使用 **-s 或--support**选项提供支持票证号。

运行完成后, 除非指定了 output 文件夹, 否则新的 tar 文件将显示在 PerfInsights 所在的文件夹中。 该文件的名称为**PerformanceDiagnostics\_yyyy-mm-dd\_hh-mm-ss-fff.zip. gz。** 可以将此文件发送给支持代理进行分析, 或在文件中打开报表以查看结果和建议。

## <a name="review-the-diagnostics-report"></a>查看诊断报告

在**PerformanceDiagnostics\_yyyy\_-MM hh-mm-ss-fff.zip**文件中, 可以找到一个 HTML 报告, 其中详细说明了 PerfInsights 的调查结果。 若要查看报表, 请展开 **"\_PerformanceDiagnostics\_" hh-mm-ss-fff.zip**文件, 然后打开**PerfInsights 报表 .html**文件。

### <a name="overview-tab"></a>概述选项卡

"**概述**" 选项卡提供了基本的运行详细信息和虚拟机信息。 "**发现**" 选项卡将显示 PerfInsights 报表所有不同部分的建议摘要。

![PerfInsights 报表的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![PerfInsights 报表的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> 分类为“高”的发现结果是可能会导致性能问题发生的已知问题。 分类为“中等”的发现结果表示配置不是最佳，不一定会导致性能问题发生。 分类“低”的发现结果只是参考性陈述。

请查看所有分类为“高”和“中等”的发现结果的相关建议和链接。 了解这些发现结果对性能造成的影响，以及有关性能优化配置的最佳做法。

### <a name="cpu-tab"></a>CPU 选项卡

**Cpu**选项卡提供 PerfInsights 运行期间的系统范围内 CPU 消耗情况的相关信息。 有关 CPU 使用时间较高和运行时间较长的 CPU 使用者的信息将有助于排查 CPU 相关的高问题。

![PerfInsights "报表 CPU" 选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>存储选项卡

“结果”部分显示有关存储的各种发现结果和建议。

**块设备**和其他相关章节 (如**分区**、 **LVM**和**MDADM**选项卡) 描述了如何配置块设备并相互相关。

!["存储" 选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
!["MDADM" 选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux 选项卡

" **Linux** " 选项卡包含有关 VM 中运行的硬件和操作系统的信息。 详细信息包括正在运行的进程的列表、有关来宾代理、PCI、CPU、驱动程序和 .LIS 驱动程序的信息。

![Linux 选项卡屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>后续步骤

可将诊断日志和报告上传到 Microsoft 支持部门供进一步审查。 使用 Microsoft 支持部门人员时, 他们可能会请求您传输由 PerfInsights 生成的输出, 以帮助进行故障排除过程。

以下屏幕截图显示了你可能会收到的消息示例：

![Microsoft 支持部门发送的示例消息屏幕截图](media/how-to-use-perfinsights-linux/support-email.png)

请遵照消息中的说明访问文件传输工作区。 为了提高安全性，首次使用时必须更改密码。

登录后, 你将看到一个对话框, 用于上传 PerfInsights 收集**的\_PerformanceDiagnostics yyyy-mm-dd\_hh-mm-ss-fff.zip gz**文件。 "

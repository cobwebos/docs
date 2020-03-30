---
title: 如何在微软 Azure 中使用 PerfInsights Linux*微软文档
description: 了解如何使用 PerfInsights 解决 Linux VM 性能问题。
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266983"
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload)是一种自助诊断工具，用于收集和分析诊断数据，并提供一个报告来帮助解决 Azure 中的 Linux 虚拟机性能问题。 PerfInsights 可以作为独立工具在受支持的虚拟机上运行，也可以直接通过[对 Azure 虚拟机使用性能诊断](performance-diagnostics.md)从门户运行。

如果遇到虚拟机性能问题，我们建议在联系支持人员之前先运行此工具。

## <a name="supported-troubleshooting-scenarios"></a>支持的故障排除方案

PerfInsights 可以收集和分析多种信息。 以下部分介绍了常见方案。

### <a name="quick-performance-analysis"></a>快速性能分析

此方案收集基本信息，如虚拟机的存储和硬件配置、各种日志，包括：

- 操作系统信息

- PCI 设备信息

- 常规来宾操作系统日志

- 配置文件

- 存储信息

- Azure 虚拟机配置（使用[Azure 实例元数据服务](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)收集）

- 正在运行的进程、磁盘、内存和 CPU 使用情况的列表

- 网络信息

这属于被动收集信息，不会影响系统。

>[!Note]
>快速性能分析方案自动包含在以下每个方案中：

### <a name="performance-analysis"></a>性能分析

此方案类似于快速性能分析，但允许捕获诊断信息的时间较长。

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights 收集了哪些类型的信息

收集有关 Linux 虚拟机、操作系统、块设备、高资源使用者、配置和各种日志的信息。 以下是更多详细信息：

- 操作系统
  - Linux 发行版和版本
  - 内核信息
  - 驱动程序信息

- 硬件
  - PCI 设备`*`|

- 进程和内存
  - 进程列表（任务名称、使用的内存、打开的文件）
  - 可用物理内存总数、可用内存和可用内存
  - 总计、可用和免费交换内存
  - 分析 CPU 捕获，并在 5 秒间隔内处理 CPU 使用率
  - 在 5 秒间隔内分析进程内存使用情况的分析捕获

- 网络  
  - 具有适配器统计信息的网络适配器列表
  - 网络路由表
  - 打开的端口和状态

- 存储
  - 阻止设备列表
  - 分区列表
  - 装载点列表
  - MDADM 卷信息
  - LVM 卷信息
  - 在 5 秒间隔内对所有磁盘进行分析捕获

- 日志
  - /var/log/messages
  - /var/日志/系统日志
  - /var/日志/克尔恩.log
  - /var/日志/cron.log
  - /var/日志/引导.log
  - /var/日志/yum.log
  - /var/日志/dpkg.log
  - /var/日志/云 init.log
  - /var/日志/云-以输出。日志
  - /var/日志/gpu 管理器.log
  - /var/log/waagent.log
  - /var/日志/azure/[扩展文件夹]/\*日志\*
  - /var/选择/微软/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /等/花
  - 过去五天的日志输出

- [Azure 虚拟机实例元数据](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] 尚未收集关于 Debian 和 SLES 分布的 PCI 信息

## <a name="run-the-perfinsights-linux-on-your-vm"></a>在 VM 上运行 PerfInsights Linux

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>在运行该工具之前，我必须了解哪些信息

#### <a name="tool-requirements"></a>工具要求

- 此工具必须在有性能问题的 VM 上运行。
- Python 2.7 必须安装在 VM 上

- 当前支持以下分发：

    | 分发               | 版本                                         |
    |----------------------------|-------------------------------------------------|
    | 甲骨文Linux服务器        | 6.10`*`= 、7.3、7.6、7.5（Oracle-数据库-Ee 13.8 市场图像）|
    | CentOS                     | 6.5`*`[ ， 7.6                                    |
    | RHEL                       | 7.2， 7.5， 8.0 |`*`                               |
    | Ubuntu                     | 14.04、16.04、18.04                               |
    | Debian                     | 8、 9、 10 |`*`                                    |
    | SLES                       | 12 SP4`*`|                                      |
    |                            |                                                   |

>[!Note]
>[`*`] 请参阅[已知问题](#known-issues)部分

### <a name="known-issues"></a>已知问题

- 默认情况下，RHEL 8 未安装 Python。 要运行 PerfInsights Linux，您必须首先安装 Python 2.7

- 来宾代理信息收集可能在 CentOS 6.x 上失败

- PCI 设备信息不会在基于 Debian 的分发上收集

- LVM 信息在某些发行版上部分收集

### <a name="how-do-i-run-perfinsights"></a>如何运行 PerfInsights

您可以通过从 Azure 门户安装 Azure 性能诊断，在虚拟机上运行 PerfInsights。 也可以将其作为独立工具运行。

>[!Note]
>PerfInsights 只需收集和分析数据。 它不对系统进行任何修改。

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>安装和运行来自 Azure 门户的 PerfInsights

有关此选项的详细信息，请参阅 Azure[性能诊断](performance-diagnostics.md)。  

#### <a name="run-perfinsights-in-standalone-mode"></a>在独立模式下运行 PerfInsights

要运行 PerfInsights 工具，请按照以下步骤操作：

1. 将[PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload)下载到虚拟机上的文件夹，并使用以下命令从终端中提取内容。

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. 导航到包含`perfinsights.py`文件的文件夹，然后运行`perfinsights.py`以查看可用的命令行参数。

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights Linux 命令线输出的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    运行 PerfInsights 方案的基本语法是：

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    可以使用以下示例运行 1 分钟的快速性能分析方案，并在 /tmp/输出文件夹下创建结果：

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    您可以使用以下示例运行性能分析方案 5 分钟，并将结果 tar 球上载到存储帐户：

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >运行方案之前，PerfInsights 会提示用户同意共享诊断信息并同意 EULA。 使用 **-a 或 -- 接受免责声明和共享诊断**选项跳过这些提示。
    >
    >如果您有 Microsoft 的有效支持票证，并且根据您正在使用的支持工程师的请求运行 PerfInsights，请确保使用 **-s 或 --支持请求**选项提供支持票证编号。

运行完成后，除非未指定输出文件夹，否则新的 tar 文件将显示在与 PerfInsights 相同的文件夹中。 文件的名称是**性能诊断\_\_yyyy-MM-dd hh-mm-s-fff.tar.gz。** 您可以将此文件发送给支持代理进行分析，或在文件内打开报表以查看调查结果和建议。

## <a name="review-the-diagnostics-report"></a>查看诊断报告

在**性能诊断\_yyyy-MM-dd\_hh-mm-s-fff.tar.gz**文件中，您可以找到一个 HTML 报告，详细说明 PerfInsights 的调查结果。 要查看报告，可以展开**性能诊断\_yyyy-MM-dd\_hh-mm-s-fff.tar.gz**文件，然后打开**PerfInsights Report.html**文件。

### <a name="overview-tab"></a>概述选项卡

**"概述"** 选项卡提供基本运行详细信息和虚拟机信息。 "**调查结果"** 选项卡显示 PerfInsights 报告所有不同部分的建议摘要。

![PerfInsights 报告的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![PerfInsights 报告的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> 分类为“高”的发现结果是可能会导致性能问题发生的已知问题。 分类为“中等”的发现结果表示配置不是最佳，不一定会导致性能问题发生。 分类“低”的发现结果只是参考性陈述。

请查看所有分类为“高”和“中等”的发现结果的相关建议和链接。 了解这些发现结果对性能造成的影响，以及有关性能优化配置的最佳做法。

### <a name="cpu-tab"></a>CPU 选项卡

**CPU**选项卡提供有关 PerfInsights 运行期间系统范围 CPU 消耗的信息。 有关 CPU 使用期高和运行时间长的 CPU 使用者的信息将有助于解决与 CPU 相关的高问题。

![PerfInsights 报告 CPU 选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>存储选项卡

“结果”部分显示有关存储的各种发现结果和建议。****

**块设备**和其他相关部分（如**分区****、LVM**和**MDADM**选项卡）描述块设备的配置方式和相互关联。

![存储选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![MDADM 选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux 选项卡

**Linux**选项卡包含有关在 VM 中运行的硬件和操作系统的信息。 详细信息包括正在运行的进程的列表以及有关来宾代理、PCI、CPU、驱动程序和 LIS 驱动程序的信息。

![Linux 选项卡的屏幕截图](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>后续步骤

可将诊断日志和报告上传到 Microsoft 支持部门供进一步审查。 当您与 Microsoft 支持人员合作时，他们可能会要求您传输 PerfInsights 生成的输出，以协助故障排除过程。

以下屏幕截图显示了你可能会收到的消息示例：

![Microsoft 支持部门发送的示例消息屏幕截图](media/how-to-use-perfinsights-linux/support-email.png)

请遵照消息中的说明访问文件传输工作区。 为了提高安全性，首次使用时必须更改密码。

登录后，您将找到一个对话框来上传 PerfInsights 收集的**性能诊断\_\_yyyy-MM-dd hh-mm-sf.tar.gz**文件。

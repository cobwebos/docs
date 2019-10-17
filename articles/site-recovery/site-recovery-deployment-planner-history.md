---
title: Azure Site Recovery 部署规划器版本历史记录
description: 已知的不同 Site Recovery 部署规划器版本修复和已知限制及其发布日期。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433407"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery 部署规划器版本历史记录

本文提供了所有版本的 Azure Site Recovery 部署规划器的历史记录，以及修复、每个版本中的已知限制及其发布日期。

## <a name="version-251"></a>版本2.51

**发布日期：2019年8月22日**

**纠正**

- 修复了部署规划器版本2.5 的成本建议问题

## <a name="version-25"></a>版本2。5

**发布日期：2019年7月29日**

**纠正**

- 对于 VMware 虚拟机和物理计算机，建议将更新为基于到托管磁盘的复制。
- 添加了对 Windows 10 （x64）、Windows 8.1 （x64）、Windows 8 （x64）、Windows 7 （x64） SP1 或更高版本的支持

## <a name="version-24"></a>版本2。4

**发布日期：2019年4月17日**

**纠正**

- 提高了操作系统兼容性，尤其是在处理基于本地化的错误时。
- 向兼容性清单添加了最多具有 20 Mbps 的数据更改率（改动）的 Vm。
- 改进的错误消息
- 添加了对 vCenter 6.7 的支持。
- 添加了对 Windows Server 2019 和 Red Hat Enterprise Linux （RHEL）工作站的支持。



## <a name="version-23"></a>版本2。3

**发布日期：2018年12月3日**

**纠正**

- 修复了阻止部署规划器使用提供的目标位置和订阅生成报表的问题。

## <a name="version-22"></a>版本2。2 

**发布日期：2018年4月25日**

**纠正**

- GetVMList 操作：
  - 修复了如果指定的文件夹不存在，导致 GetVMList 失败的问题。 现在，它将创建默认目录，或创建在 outputfile 参数中指定的目录。
  - 为 GetVMList 添加了更详细的失败原因。
- 在部署规划器报表的兼容 Vm 表中添加了 VM 类型信息作为列。
- Hyper-v 到 Azure 的灾难恢复：
  - 从分析中排除了包含共享 Vhd 和传递磁盘的 Vm。 Startprofiling 操作在控制台中显示已排除的 Vm 列表。
  - 已将超过64个磁盘的 Vm 添加到不兼容的 Vm 列表。
  - 更新了初始复制（IR）和增量复制（DR）压缩因素。
  - 增加了对 SMB 存储的有限支持。

## <a name="version-21"></a>版本2。1

**发布日期：2018年1月3日**

**纠正**

- 已更新 Excel 报表。
- 修复了 GetThroughput 操作中的 bug。
- 添加了限制要分析或生成报告的 Vm 数的选项。 默认限制为1000个虚拟机。
- VMware 到 Azure 的灾难恢复：
  - 修复了进入不兼容表的 Windows Server 2016 VM 的问题。 
  - 已更新可扩展固件接口（EFI） Windows Vm 的兼容性消息。
- 更新 VMware 到 Azure 和 Hyper-v 到 Azure，每个 VM 的 VM 数据变动限制。 
- 提高了 VM 列表文件分析的可靠性。

## <a name="version-201"></a>版本 2.0.1

**发布日期：2017年12月7日**

**纠正**

- 添加了建议以优化网络带宽。

## <a name="version-20"></a>版本2。0

**发布日期：2017年11月28日**

**纠正**

- 添加了对 Hyper-v 到 Azure 灾难恢复的支持。
- 增加了成本计算器。
- 添加了 VMware 到 Azure 灾难恢复的操作系统版本检查，以确定 VM 是否兼容或不兼容以进行保护。 该工具使用 vCenter 服务器为该 VM 返回的 OS 版本字符串。 它是用户在 VMware 中创建 VM 时选择的来宾操作系统版本。

**已知限制：**

- 对于从 Hyper-v 到 Azure 的灾难恢复，名称包含以下字符的 VM 不受支持： `,`、`"`、`[`、`]` 和 @no__t。 如果对其进行了分析，则报告生成将失败或结果不正确。
- 对于 VMware 到 Azure 的灾难恢复，不支持名称包含逗号的 VM。 如果对其进行了分析，则报告生成将失败或产生不正确的结果。

## <a name="version-131"></a>版本 1.3.1

**发布日期：2017年7月19日** 

**纠正**

- 添加了对大型磁盘（> 1 TB）的支持。 现在，你可以使用部署规划器来规划磁盘大小大于 1 TB （最大为 4095 GB）的虚拟机的复制。
阅读有关 [Azure Site Recovery 中的大型磁盘支持](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)的更多内容

## <a name="version-13"></a>版本1。3

**发布日期：5月9日，2017**

**纠正**

- 在报告生成中添加了对托管磁盘的支持。 根据是否为故障转移/测试故障转移选择了托管磁盘，可放置到单个存储帐户的 Vm 的数量。

## <a name="version-12"></a>版本1。2

**发布日期：2017年4月7日**

**纠正**

- 为每个 VM 添加了启动类型（BIOS 或 EFI）检查，以确定 VM 是否兼容或不兼容以进行保护。
- 在 "兼容 Vm 和不兼容的 Vm" 工作表中添加了每个虚拟机的 OS 类型信息。
- 添加了对美国政府和中国 Microsoft Azure 地区的 GetThroughput 操作的支持。
- 添加了针对 vCenter 和 ESXi Server 的更多先决条件检查。
- 修复了在区域设置设置为非英语时生成错误报表的问题。

## <a name="version-11"></a>版本 1.1

**发布日期：2017年3月9日**

**纠正**

- 修复了两个或多个 Vm 在不同 vCenter ESXi 主机上具有相同名称或 IP 地址的 vm 时阻止分析 Vm 的问题。
- 修复了一个问题，该问题导致对兼容 Vm 和不兼容的 Vm 工作表禁用复制和搜索。

## <a name="version-10"></a>版本1。0

**发布日期：2017年2月23日**

**已知限制：**

- 仅支持 VMware 到 Azure 灾难恢复方案。 对于 Hyper-v 到 Azure 的灾难恢复方案，请使用[hyper-v 容量规划器工具](./site-recovery-capacity-planning-for-hyper-v-replication.md)。
- 不支持美国政府和中国 Microsoft Azure 地区的 GetThroughput 操作。
- 如果 vCenter 服务器中有两个或更多个 Vm 跨不同的 ESXi 主机具有相同的名称或 IP 地址，则工具 cann't 分析 Vm。
在此版本中，该工具将跳过 VMListFile 中针对重复 VM 名称或 IP 地址的分析。 解决方法是使用 ESXi 主机而不是 vCenter 服务器来分析 VM。 确保为每个 ESXi 主机运行一个实例。

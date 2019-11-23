---
title: Azure Site Recovery 部署规划器版本历史记录
description: 已知不同的 Site Recovery 部署规划器版本修复、已知限制及其发布日期。
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

本文提供 Azure Site Recovery 部署规划器的所有版本历史记录和修复，以及每个版本的已知限制和发布日期。

## <a name="version-251"></a>版本2.51

**发布日期：2019年8月22日**

**修复：**

- 修复了部署规划器版本2.5 的成本建议问题

## <a name="version-25"></a>版本 2.5

**发布日期：2019年7月29日**

**修复：**

- 对于 VMware 虚拟机和物理计算机，将建议更新为基于到托管磁盘的复制。
- 添加了对 Windows 10 (x64)、Windows 8.1 (x64)、Windows 8 (x64)、Windows 7 (x64) SP1 或更高版本的支持

## <a name="version-24"></a>版本 2.4

**发布日期：2019年4月17日**

**修复：**

- 改进了操作系统兼容性，特别是处理基于本地化的错误方面的兼容性。
- 在兼容性清单中添加了最大支持 20 Mbps 数据更改率（变动率）的 VM。
- 改进了错误消息
- 添加了对 vCenter 6.7 的支持。
- 添加了对 Windows Server 2019 和 Red Hat Enterprise Linux （RHEL）工作站的支持。



## <a name="version-23"></a>版本 2.3

**发布日期：2018年12月3日**

**修复：**

- 修复了阻止部署规划器使用提供的目标位置和订阅生成报告的问题。

## <a name="version-22"></a>版本 2.2 

**发布日期：2018年4月25日**

**修复：**

- GetVMList 操作：
  - 修复了如果指定的文件夹不存在，导致 GetVMList 失败的问题。 它现在会创建默认目录，或创建 outputfile 参数中指定的目录。
  - 添加了 GetVMList 的更详细失败原因。
- 在部署规划器报告的兼容 VM 工作表中以列的形式添加了 VM 类型信息。
- Hyper-V 到 Azure 的灾难恢复：
  - 已从分析中排除使用共享 VHD 和直通磁盘的 VM。 Startprofiling 操作会在控制台中显示已排除的 VM 列表。
  - 在不兼容的 VM 列表中添加了包含 64 个以上磁盘的 VM。
  - 更新了初始复制 (IR) 和增量复制 (DR) 压缩因子。
  - 添加了对 SMB 存储的有限支持。

## <a name="version-21"></a>版本 2.1

**发布日期：2018年1月3日**

**修复：**

- 更新了 Excel 报告。
- 修复了 GetThroughput 操作的 bug。
- 添加了相应的选项用于限制要分析或要生成报告的 VM 数。 默认限制为 1,000 个 VM。
- VMware 到 Azure 的灾难恢复：
  - 修复了将 Windows Server 2016 VM 加入不兼容表的问题。 
  - 更新了可扩展固件接口 (EFI) Windows VM 的兼容性消息。
- 更新了从 VMware 灾难恢复到 Azure 以及从 Hyper-V 灾难恢复到 Azure 时，每个 VM 的 VM 数据变动率限制。 
- 改进了 VM 列表文件分析的可靠性。

## <a name="version-201"></a>版本 2.0.1

**发布日期：2017年12月7日**

**修复：**

- 添加了有关优化网络带宽的建议。

## <a name="version-20"></a>版本 2.0

**发布日期：2017年11月28日**

**修复：**

- 添加了 Hyper-V 到 Azure 的灾难恢复的支持。
- 添加了成本计算器。
- 为 VMware 到 Azure 的灾难恢复添加了 OS 版本检查，以确定 VM 是否与保护功能兼容。 该工具会使用 vCenter 服务器针对该 VM 返回的 OS 版本字符串。 它是用户在 VMware 中创建 VM 时选择的来宾操作系统版本。

**已知限制：**

- 对于 Hyper-V 到 Azure 的灾难恢复，不支持名称中包含 `,`、`"`、`[`、`]` 和 ``` ` ``` 等字符的 VM。 分析后，报告生成将会失败或包含错误的结果。
- 对于 VMware 到 Azure 的灾难恢复，不支持名称中包含逗号的 VM。 分析后，报告生成将会失败或包含错误的结果。

## <a name="version-131"></a>版本 1.3.1

**发布日期：2017年7月19日** 

**修复：**

- 添加了在报告生成中包含大型磁盘 (> 1TB) 的支持。 现在可以使用部署规划器来计划磁盘大小超出 1 TB（最大为 4095 GB）的虚拟机的复制。
阅读有关 [Azure Site Recovery 中的大型磁盘支持](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)的更多内容

## <a name="version-13"></a>版本 1.3

**发布日期：5月9日，2017**

**修复：**

- 添加了在报告生成中包含托管磁盘的支持。 可以放置到单个存储帐户的 VM 数的计算取决于是否为故障转移/测试故障转移选择了托管磁盘。

## <a name="version-12"></a>版本 1.2

**发布日期：2017年4月7日**

**修复：**

- 对每个 VM 添加了启动类型（BIOS 或 EFI）检查，以确定 VM 是否与保护功能兼容。
- 在兼容的 VM 和不兼容的 VM 工作表中添加了每个虚拟机的 OS 类型信息。
- 添加了对美国政府和中国 Microsoft Azure 地区的 GetThroughput 操作的支持。
- 添加了针对 vCenter 和 ESXi Server 的更多先决条件检查。
- 修复了将区域设置指定为非英语时生成错误报告的问题。

## <a name="version-11"></a>版本 1.1

**发布日期：2017年3月9日**

**修复：**

- 修复了当有两个或更多个具有相同名称或 IP 地址的 VM 跨不同的 vCenter ESXi 主机时阻止分析 VM 的问题。
- 修复了导致对兼容 VM 和不兼容 VM 工作表禁用复制与搜索的问题。

## <a name="version-10"></a>版本 1.0

**发布日期：2017年2月23日**

**已知限制：**

- 仅支持 VMware 到 Azure 的灾难恢复方案。 对于 Hyper-V 到 Azure 的灾难恢复方案，请使用 [Hyper-V 容量规划器工具](./site-recovery-capacity-planning-for-hyper-v-replication.md)。
- 不支持美国政府和中国 Microsoft Azure 地区的 GetThroughput 操作。
- 如果 vCenter 服务器中有两个或更多个 VM 跨不同的 ESXi 主机使用相同的名称或 IP 地址，则该工具无法分析 VM。
在此版本中，该工具将跳过 VMListFile 中针对重复 VM 名称或 IP 地址的分析。 解决方法是使用 ESXi 主机而不是 vCenter 服务器来分析 VM。 确保运行每个 ESXi 主机的一个实例。

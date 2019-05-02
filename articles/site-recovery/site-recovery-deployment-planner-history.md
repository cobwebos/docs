---
title: Azure 站点恢复部署规划器版本历史记录
description: 已知的不同站点恢复部署规划器版本修补程序，以及发布日期的已知的限制。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927367"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure 站点恢复部署规划器版本历史记录

本文提供所有版本的 Azure Site Recovery Deployment Planner 修复，已知中每个的限制和发布日期以及历史的记录。

## <a name="version-24"></a>版本 2.4

**发布日期：2019 年 4 月 17日日**

**修补程序：**

- 改进了运行系统的兼容性，特别是，当处理基于本地化的错误。
- 添加了的 Vm 与最多 20 Mbps 的数据更改率 （变动） 为兼容性清单。
- 改进了的错误消息
  - 添加了的对 vCenter 6.7。
  - 添加了的对 Windows Server 2019 和 Red Hat Enterprise Linux (RHEL) 的工作站。



## <a name="version-23"></a>版本 2.3

**发布日期：2018 年 12 月 3日日**

**修补程序：**

- 修复了阻止部署规划器生成具有提供的目标位置和订阅的报表的问题。

## <a name="version-22"></a>版本 2.2 

**发布日期：2018 年 4 月 25日日**

**修补程序：**

- GetVMList 操作：
  - 修复了导致 GetVMList 若要在未指定的文件夹不存在问题。 它现在将创建默认目录，或创建输出文件参数中指定的目录。
  - 添加更多详细 GetVMList 的失败原因。
- 为兼容的 Vm 工作表的部署规划器报表中的列添加了的 VM 类型信息。
- Hyper-v 到 Azure 的灾难恢复：
  - 具有共享 Vhd 和传递的排除的 Vm 磁盘从分析。 Startprofiling 操作控制台中显示已排除的 Vm 的列表。
  - 添加的 Vm 64 个以上磁盘的不兼容的 Vm 列表。
  - 更新的初始复制 (IR) 和增量复制 (DR) 压缩因子。
  - 添加了对 SMB 存储的有限的支持。

## <a name="version-21"></a>版本 2.1

**发布日期：2018 年 1 月 3日日**

**修补程序：**

- 更新的 Excel 报表。
- 在 GetThroughput 操作中的已修复的 bug。
- 添加了的选项来限制要分析或生成报告的 Vm 数量。 默认限制为 1,000 个 Vm。
- VMware 到 Azure 的灾难恢复：
  - 修复了的 Windows Server 2016 VM 进入不兼容的表。 
  - 可扩展固件接口 (EFI) Windows Vm 的更新的兼容性消息。
- 更新 VMware 到 Azure 和 HYPER-V 到 Azure，VM 数据变动量限制每个 VM。 
- 分析的 VM 列表文件的改进了的可靠性。

## <a name="version-201"></a>版本 2.0.1

**发布日期：2017 年 12 月 7日日**

**修补程序：**

- 添加了的建议，以优化网络带宽。

## <a name="version-20"></a>2.0 版

**发布日期：2017 年 11 月 28日日**

**修补程序：**

- 添加了的对 HYPER-V 到 Azure 的灾难恢复。
- 添加了的成本计算器。
- 添加了的 OS 版本检查的 VMware 到 Azure 的灾难恢复，以确定 VM 是否兼容还是不兼容的保护。 该工具使用由 vCenter 服务器返回为该 VM 的 OS 版本字符串。 它是来宾操作系统版本在 VMware 中创建 VM 时选择该用户。

**已知的限制：**

- 对于 HYPER-V 到 Azure 的灾难恢复、 VM 名称包含字符如： `,`， `"`， `[`， `]`，和``` ` ```不受支持。 如果配置文件时，报表生成将失败，或将具有不正确的结果。
- 对于 VMware 到 Azure 的灾难恢复，不支持其名称包含逗号的 VM。 如果配置文件时，报告生成失败，或将具有不正确的结果。

## <a name="version-131"></a>版本 1.3.1

**发布日期：2017 年 7 月 19日日** 

**修补程序：**

- 添加了的对在生成报告中的大型磁盘 (> 1 TB)。 现在可以使用 Deployment Planner 来计划磁盘大小超过 1 TB 的虚拟机的复制 (最大 4095 GB)。
阅读有关 [Azure Site Recovery 中的大型磁盘支持](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)的更多内容

## <a name="version-13"></a>1.3 版

**发布日期：2017 年 5 月 9**

**修补程序：**

- 添加了的对在生成报告中的托管磁盘。 可以放置到单个存储帐户的 Vm 数量是根据计算如果托管的磁盘用于故障转移/测试故障转移。

## <a name="version-12"></a>1.2 版

**发布日期：2017 年 4 月 7日日**

**修补程序：**

- 已添加的启动类型 （BIOS 或 EFI） 检查每个 VM，以确定 VM 是否兼容还是不兼容的保护。
- 添加了的 OS 类型兼容的 Vm 和不兼容 Vm 工作表中的每个虚拟机的信息。
- 添加了的对适用于美国政府部门和 Microsoft Azure 中国地区 GetThroughput 操作。
- 添加了针对 vCenter 和 ESXi Server 的更多先决条件检查。
- 修复不正确的区域设置设置为非英语时生成的报表。

## <a name="version-11"></a>版本 1.1

**发布日期：2017 年 3 月 9日日**

**修补程序：**

- 修复了阻止跨不同的 vCenter ESXi 主机有两个或多个具有相同的名称或 IP 地址的 Vm 在分析 Vm 问题。
- 修复了导致复制和搜索要禁用兼容 Vm 和不兼容 Vm 工作表的问题。

## <a name="version-10"></a>版本 1.0

**发布日期：2017 年 2 月 23日日**

**已知的限制：**

- 仅对 VMware 到 Azure 的灾难恢复方案的支持。 对于 HYPER-V 到 Azure 灾难恢复方案，请使用[的 HYPER-V capacity planner 工具](./site-recovery-capacity-planning-for-hyper-v-replication.md)。
- 不支持 GetThroughput 操作适用于美国政府部门和 Microsoft Azure 中国地区。
- 该工具 cann't 如果 vCenter 服务器中有相同的名称或 IP 地址的两个或多个 Vm 跨不同的 ESXi 主机的虚拟机配置文件。
在此版本中，该工具将跳过 VMListFile 中针对重复 VM 名称或 IP 地址的分析。 解决方法是使用 ESXi 主机而不是 vCenter 服务器来分析 VM。 请确保针对每个 ESXi 主机运行一个实例。

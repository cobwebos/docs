---
title: Azure Site Recovery 中的故障回复 | Microsoft Docs
description: 本文概述了各种类型的故障回复，以及在使用 Azure Site Recovery 服务故障回复到本地时要考虑的注意事项。
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanki
ms.openlocfilehash: 372a7867b47960338d7a1bf7e646fb9fffbe72e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
ms.locfileid: "29803546"
---
# <a name="overview-of-failback"></a>故障回复概述

故障转移到 Azure 以后，可以故障回复到本地站点。 使用 Azure Site Recovery 可以执行两种不同类型的故障回复： 

- 故障回复到原始位置 
- 故障回复到备用位置

故障转移 VMware 虚拟机后，可以故障回复到同一个本地源虚拟机（如果仍然存在）。 在这种情况下，只会复制更改。 这种情况称为**原始位置恢复**。 如果本地虚拟机不存在，则这种情况是一种**备用位置恢复**。

> [!NOTE]
> 仅可故障回复到原始 vCenter 和配置服务器。 无法部署新配置服务器并通过它进行故障回复。 此外，无法向现有配置服务器添加新 vCenter 并故障回复到新 vCenter。

## <a name="original-location-recovery-olr"></a>原始位置恢复 (OLR)
如果选择故障回复到原始虚拟机，需要满足以下条件：

* 如果虚拟机由 vCenter 服务器管理，主目标的 ESX 主机应该可以访问虚拟机数据存储。
* 如果虚拟机位于 ESX 主机上，但不由 vCenter 管理，则虚拟机的硬盘必须位于主目标的主机可访问的数据存储中。
* 如果虚拟机位于 ESX 主机上但不使用 vCenter，则应完成针对主目标的 ESX 主机的发现操作，才能进行重新保护。 若要对物理服务器进行故障回复，此规则也同样适用。
* 可以故障回复到虚拟存储区网络 (vSAN) 或基于原始设备映射 (RDM) 的磁盘（如果磁盘已存在并且已连接到本地虚拟机）。

> [!IMPORTANT]
> 请务必启用 disk.enableUUID= TRUE，以便故障回复期间 Azure Site Recovery 服务能够识别将向其写入未完成更改的虚拟机上的原始 VMDK。 如果此值未设置为 TRUE，则该服务会尽力尝试识别相应的本地 VMDK。 如果找不到合适的 VMDK，它会创建额外的磁盘，并将数据写入到该磁盘。

## <a name="alternate-location-recovery-alr"></a>备用位置恢复 (ALR)
如果在重新保护本地虚拟机之前该虚拟机不存在，则该方案称为备用位置恢复。 重新保护工作流将重新创建本地虚拟机。 这还会导致完整数据下载。

* 故障回复到备用位置时，会将虚拟机恢复到主目标服务器所部署到的同一 ESX 主机。 用于创建磁盘的数据存储将与重新保护虚拟机时选择的数据存储相同。
* 只能故障回复到虚拟机文件系统 (VMFS) 或 vSAN 数据存储。 如果有 RDM，重新保护和故障回复将不起作用。
* 重新保护涉及一次较大的初始数据传输，并会进行更改。 之所以要执行此过程，是因为本地没有虚拟机。 需要复制回完整的数据。 此重新保护所需的时间还比原始位置恢复长。
* 无法故障回复到基于 RDM 的磁盘。 只能在 VMFS/vSAN 数据存储中创建新的虚拟机磁盘 (VMDK)。

> [!NOTE]
> 故障转移到 Azure 时，物理计算机只能故障回复为 VMware 虚拟机。 这需要执行与备用位置恢复相同的工作流。 确保除了需要故障回复到的必要 ESX/ESXi 主机之外，还发现至少一台主目标服务器。

## <a name="next-steps"></a>后续步骤

按照步骤执行[故障回复操作](vmware-azure-failback.md)。


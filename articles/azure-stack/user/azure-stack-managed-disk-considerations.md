---
title: Azure Stack 中托管磁盘的差异和注意事项 | Microsoft Docs
description: 了解使用 Azure Stack 中的托管磁盘时的差异和注意事项。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: e86e1d862644aa143046045e74ec994d42a5598e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050160"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 托管磁盘： 差异和注意事项

本文汇总了的已知的差异[Azure Stack 托管磁盘](azure-stack-manage-vm-disks.md)并[适用于 Azure 托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

托管磁盘通过管理与 VM 磁盘关联的[存储帐户](../azure-stack-manage-storage-accounts.md)简化了 IaaS VM 的磁盘管理。

> [!Note]  
> 可从版本 1808 Azure Stack 上的托管的磁盘。
  

## <a name="cheat-sheet-managed-disk-differences"></a>速查表：托管磁盘的差异

| Feature | Azure（公有云） | Azure Stack |
| --- | --- | --- |
|静态数据加密 |Azure 存储服务加密 (SSE)、Azure 磁盘加密 (ADE)     |BitLocker 128 位 AES 加密      |
|映像          | 支持托管自定义映像 |尚不支持|
|备份选项 |支持 Azure 备份服务 |尚不支持 |
|灾难恢复选项 |支持 Azure Site Recovery |尚不支持|
|磁盘类型     |高级 SSD、 标准 SSD （预览版） 和标准 HDD |高级 SSD、标准 HDD |
|高级磁盘  |完全支持 |可部署，但无性能限制或保证  |
|高级磁盘 IOPS  |取决于磁盘大小  |每个磁盘 2300 IOPS |
|高级磁盘吞吐量 |取决于磁盘大小 |每个磁盘 145 MB/秒 |
|磁盘大小  |Azure 高级磁盘：P4 (32 GiB) 到 P80 (32 TiB)<br>Azure 标准 SSD 盘：E10 (128 GiB) 到 E80 (32 TiB)<br>Azure 标准 HDD 磁盘：S4 (32 GiB) 到 S80 (32 TiB) |M4:32 GiB<br>M6:64 GiB<br>M10:128 GiB<br>M15:256 GiB<br>M20:512 GiB<br>M30:1024 GiB |
|磁盘快照复制|快照 Azure 托管磁盘附加到正在运行的 VM 支持|尚不支持 |
|磁盘性能分析 |支持的聚合指标和每磁盘指标 |尚不支持 |
|迁移      |提供工具，用于从现有的非托管 Azure 资源管理器 Vm 而无需重新创建 VM 迁移  |尚不支持 |

> [!NOTE]  
> 托管磁盘 IOPs 和吞吐量在 Azure Stack 中的是 cap 数字而不是一个预配的数字，其可能受到硬件和在 Azure Stack 中运行的工作负荷。

## <a name="metrics"></a>度量值

存储指标也有一些差异：

- 使用 Azure Stack，存储度量值中的事务数据不区分内部或外部网络带宽。
- Azure Stack 事务数据在存储度量值不包括与装入的磁盘的虚拟机访问。

## <a name="api-versions"></a>API 版本

Azure Stack 托管磁盘支持以下 API 版本：

- 2017-03-30

## <a name="known-issues"></a>已知问题

在应用后[1811年更新](../azure-stack-update-1811.md)，部署包含托管磁盘的 Vm 时，您可能会遇到以下问题：

- 如果 1808年更新，使用托管磁盘部署 VM 之前创建的订阅可能会失败并显示内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
   1. 在租户门户中转到“订阅”，找到相应订阅。 依次单击“资源提供程序”、“Microsoft.Compute”、“重新注册”。
   2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。
- 如果已配置多租户环境中，在与来宾目录关联的订阅中部署虚拟机内部的错误消息可能会失败。 若要解决此错误，请按照中的步骤[这篇文章](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)重新配置每个来宾目录。


## <a name="next-steps"></a>后续步骤

- [了解 Azure Stack 虚拟机](azure-stack-compute-overview.md)

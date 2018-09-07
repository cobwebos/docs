---
title: 差异和注意事项的 Azure Stack 中的托管磁盘 |Microsoft Docs
description: 使用 Azure Stack 中的托管磁盘时，了解有关差异和注意事项。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 4fd2a26d9119e52fc75918abc1ca97e6f9888169
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028797"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 托管磁盘： 差异和注意事项
本文汇总了 Azure Stack 托管磁盘和 azure 托管磁盘的已知的差异。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

托管的磁盘通过管理简化了 IaaS Vm 的磁盘管理[存储帐户](/azure/azure-stack/azure-stack-manage-storage-accounts)与 VM 磁盘关联。
  

## <a name="cheat-sheet-virtual-machine-differences"></a>速查表：虚拟机的差异

| 功能 | Azure（公有云） | Azure Stack |
| --- | --- | --- |
|对于静态数据加密 |Azure 存储服务加密 (SSE)，Azure 磁盘加密 (ADE)     |BitLocker 128 位 AES 加密      |
|映像          | 支持托管自定义映像 |尚不支持|
|备份选项 |支持 Azure 备份服务 |尚不支持 |
|灾难恢复选项 |支持 Azure Site Recovery |尚不支持|
|磁盘类型     |高级 SSD、 标准 SSD （预览版） 和标准 HDD |高级 SSD，标准 HDD |
|高级磁盘  |完全支持 |可部署，但无性能限制或保证  |
|高级磁盘  |IOPs  |取决于磁盘大小 2300 IOPs，每个磁盘 |
|高级磁盘吞吐量 |取决于磁盘大小 |每个磁盘 145 MB/秒 |
|磁盘最大大小  |4 TB       |1 TB       |
|磁盘性能分析 |聚合的度量值和每个磁盘支持的指标 |尚不支持 |
|迁移      |提供工具，用于从现有的非托管 Azure 资源管理器 Vm 而无需重新创建 VM 迁移  |尚不支持 |


## <a name="metrics"></a>度量值
存储指标也有一些差异：
- 使用 Azure Stack，存储度量值中的事务数据不区分内部或外部网络带宽。
- Azure Stack 事务数据在存储度量值不包括与装入的磁盘的虚拟机访问。


## <a name="api-versions"></a>API 版本
Azure Stack 托管磁盘支持以下 API 版本：
- 2017-03-30


## <a name="next-steps"></a>后续步骤
[了解 Azure Stack 虚拟机](azure-stack-compute-overview.md)

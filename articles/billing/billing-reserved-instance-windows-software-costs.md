---
title: Azure 保留 VM 实例 Windows 软件成本 | Microsoft Docs
description: 了解预订虚拟机实例成本中不包含哪些 Windows 软件计量。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 20fd3c6c118b33ca91d717d30d77bb9c7843f4e6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>保留实例未包含的 Windows 软件成本

如果你对预订实例虚拟机没有 Azure 混合使用权益，则会针对以下部分中列出的 Windows 软件计量向你收取费用。

## <a name="windows-software-meters-not-included-in-reserved-instance-cost"></a>预订实例成本中未包含的 Windows 软件计量

| 计量 ID | 使用情况文件中的计量名称 | VM 使用的系列 |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | 保留-Windows Server 突增（1 核心） | B 系列 |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | 保留-Windows Server 突增（2 核心） | B 系列 |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | 保留-Windows Server 突增（4 核心） | B 系列 |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | 保留-Windows Server 突增（8 核心） | B 系列 |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | 保留-Windows Server（1 核心） | B 系列以外的所有系列 |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | 保留-Windows Server（2 核心） | B 系列以外的所有系列 |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | 保留-Windows Server（4 核心） | B 系列以外的所有系列 |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | 保留-Windows Server（6 核心） | B 系列以外的所有系列 |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | 保留-Windows Server（8 核心） | B 系列以外的所有系列 |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | 保留-Windows Server（12 核心） | B 系列以外的所有系列 |
| 02968a6b-1654-4495-ada6-13f378ba7172 | 保留-Windows Server（16 核心） | B 系列以外的所有系列 |
| 175434d8-75f9-474b-9906-5d151b6bed84 | 保留-Windows Server（20 核心） | B 系列以外的所有系列 |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | 保留-Windows Server（24 核心） | B 系列以外的所有系列 |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | 保留-Windows Server（32 核心） | B 系列以外的所有系列 |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | 保留-Windows Server（40 核心） | B 系列以外的所有系列 |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | 保留-Windows Server（64 核心） | B 系列以外的所有系列 |
| da612742-e7cc-4ca3-9334-0fb7234059cd | 保留-Windows Server（72 核心） | B 系列以外的所有系列 |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | 保留-Windows Server（128 核心） | B 系列以外的所有系列 |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | 保留-Windows Server（256 核心） | B 系列以外的所有系列 |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | 保留-Windows Server（96 核心） | B 系列以外的所有系列 |

可以通过 Azure RateCard API 来获取上述每个计量的成本。 有关如何获取 azure 计量的费率的信息，请参阅[获取 Azure 订阅中使用的资源的价格和元数据信息](https://msdn.microsoft.com/library/azure/mt219004)。

## <a name="next-steps"></a>后续步骤
若要了解有关预订虚拟机实例的详细信息，请参阅以下文章。

- [通过预订 VM 实例预付虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理预订虚拟机实例](billing-manage-reserved-vm-instance.md)
- [通过预订虚拟机实例节省虚拟机资金](billing-save-compute-costs-reservations.md)
- [了解如何应用预订虚拟机实例折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业许可登记表的预订实例使用情况](billing-understand-reserved-instance-usage-ea.md)

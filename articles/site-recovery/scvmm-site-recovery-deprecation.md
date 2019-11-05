---
title: 即将使用 Hyper-v 在客户拥有的站点之间进行灾难恢复，在由 SCVMM 管理的站点之间过时 |Microsoft Docs
description: 有关即将发生的客户所拥有的站点的灾难恢复的详细信息，请使用 Hyper-v 在 SCVMM 管理的站点之间以及备用选项
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 68881d57f251d2f26996b2a837780106326ec1d3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492293"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>即将使用 Hyper-v 在客户拥有的站点之间以及通过 SCVMM 管理的站点之间的灾难恢复到 Azure

本文介绍 Azure Site Recovery 支持的下列方案中即将推出的弃用计划、相应的含意以及可用于客户的替代选项。 

- 客户拥有的站点之间由 SCVMM 管理的 Hyper-v Vm 之间的 DR 
- 将 SCVMM 管理的 Hyper-v Vm 灾难恢复到 Azure

> [!IMPORTANT]
> 这些方案当前已标记为不推荐使用，客户预计会尽早采取修正措施，以避免对其环境造成任何中断。 
 

## <a name="what-changes-should-you-expect"></a>你需要做哪些更改？

- 从11月2019开始，这些方案不允许使用 boardings 的新用户。 **现有的复制和管理操作**（包括故障转移、测试故障转移、监视等）**不会受到影响**。

- 一旦不推荐使用方案，除非客户遵循建议的步骤，否则会出现以下问题。

    - 客户拥有的站点之间由 SCVMM 管理的 Hyper-v Vm 之间的 DR：复制将继续工作，因为 Hyper-v 副本的基础功能将继续工作，但客户将无法查看、管理或执行任何 DR 相关操作在 Azure 门户中通过 Azure Sire 恢复体验。 
    - 将 SCVMM 管理的 Hyper-v Vm 灾难恢复到 Azure：现有复制将中断，客户无法通过 Azure Site Recovery 查看、管理或执行任何 DR 相关操作


## <a name="recommended-actions-to-be-taken"></a>建议采取的操作

在方案被弃用后，客户必须使用以下选项来确保其 DR 策略不会受到影响。 

- 选择[开始使用 Azure 作为 hyper-v 主机上的 vm 的 DR 目标](hyper-v-azure-tutorial.md)。

> [!IMPORTANT]
> 请注意，你的本地环境仍可以有 SCVMMM，但你会将 ASR 配置为仅引用 Hyper-v 主机。

- 选择继续使用站点到站点复制，但使用基础[Hyper-v 副本解决方案](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)，但将无法使用 Azure 门户中的 Azure Site Recovery 来管理 DR 配置。 


## <a name="next-steps"></a>后续步骤
规划弃用，并选择最适合你的基础结构和业务的替代选项。 如果你有关于此问题的任何查询，请联系 Microsoft 支持部门


---
title: "复查使用 Azure Site Recovery 执行 Hyper-V 到 Azure 复制（无 System Center VMM）的先决条件 | Microsoft Docs"
description: "介绍了使用 Azure Site Recovery 设置从本地 Hyper-V VM 到 Azure 的复制、故障转移和恢复时的先决条件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 63ad1dcc5a069a9958e56c8260f9aa208fafc645
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017

---

# 步骤 2：复查 Hyper-V（无 VMM）到 Azure 复制的先决条件
<a id="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication" class="xliff"></a>

下表对先决条件进行了汇总。


**先决条件** | **详细信息** 
--- | --- 
**Azure** | 了解 [Azure 要求](site-recovery-prereq.md#azure-requirements)。
**本地服务器** | [详细了解](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)本地 Hyper-V 主机的要求。
**本地 Hyper-V VM** | 想要复制的虚拟机应正在运行[受支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，并且符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**Azure URL** | Hyper-V 主机需访问以下 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/></br> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/></br> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。



## 后续步骤
<a id="next-steps" class="xliff"></a>

- 如果执行的是完整部署，请转到[步骤 3：规划容量](hyper-v-site-walkthrough-capacity.md)
- 如果执行的是简单的测试部署，请转到[步骤 4：规划网络](hyper-v-site-walkthrough-network.md)。


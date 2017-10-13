---
title: "为复制到 Azure 准备 Hyper-V 主机（不含 System Center VMM）| Microsoft Docs"
description: "介绍如何使用 Azure Site Recovery 为复制到 Azure 准备 Hyper-V 主机"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>步骤 6：为复制到 Azure 准备 Hyper-V 主机

使用本文中的说明准备本地 Hyper-V 主机以与 Azure Site Recovery 交互。

阅读本文后，欢迎在页面底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="prepare-hosts"></a>准备主机

- 确保 Hyper-V 主机符合[先决条件](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm)。
- 确保主机可以访问所需的 URL：

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。
- 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
- 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。

在 Site Recovery 部署过程中，添加包含要复制到 Hyper-V 站点的 VM 的 Hyper-V 主机。 Site Recovery 提供程序和恢复服务代理安装在每个主机上。 在恢复服务保管库中注册 Hyper-V 站点。

## <a name="next-steps"></a>后续步骤

转到[步骤 7：创建保管库](hyper-v-site-walkthrough-create-vault.md)


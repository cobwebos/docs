---
title: "准备 System Center VMM 以将 Hyper-V 复制到 Azure | Microsoft Docs"
description: "介绍如何准备 System Center VMM 以使用 Azure Site Recovery 将 Hyper-V 复制到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2017
---
# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>步骤 6：准备 VMM 服务器和 Hyper-V 主机以将 Hyper-V 复制到 Azure

为部署设置 [Azure 组件](vmm-to-azure-walkthrough-prepare-azure.md)后，请使用本文中的说明准备本地 VMM 服务器和 Hyper-V 主机，以便与 Azure Site Recovery 进行交互。

阅读本文后，欢迎在页面底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="prepare-vmm-servers"></a>准备 VMM 服务器

- 需要至少一台满足 Site Recovery 复制支持要求的 VMM 服务器 (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。
- 请确保已为[网络映射](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)准备好 VMM 服务器。
- 请确保 VMM 服务器可访问这些 URL

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。
- 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
- 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。

在 Site Recovery 部署期间，需下载 Site Recovery 提供程序并将其安装在每个 VMM 服务器上。 在恢复服务保管库中注册 VMM 服务器。




## <a name="next-steps"></a>后续步骤

转到[步骤 7：创建保管库](vmm-to-azure-walkthrough-create-vault.md)


---
title: 使用 Azure Site Recovery 执行 VMware 到 Azure 的灾难恢复时的配置服务器要求 | Microsoft Docs
description: 本文介绍了为了使用 Azure Site Recovery 执行 VMware 到 Azure 的灾难恢复而部署配置服务器时的支持和要求。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: d83b99ea540d6232f4c0786d3a743f97332e1c9f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792312"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>VMware 到 Azure 的灾难恢复的配置服务器要求

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要部署本地配置服务器。

- 配置服务器协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。
- [详细了解](vmware-azure-architecture.md)配置服务器组件和流程。

## <a name="configuration-server-deployment"></a>配置服务器部署

为实现 VMware VM 到 Azure 的灾难恢复，你将配置服务器部署为 VMware VM。

- Site Recovery 提供了从 Azure 门户中下载 OVA 模板，并导入 vCenter Server 以设置配置服务器 VM。
- 使用 OVA 模板部署配置服务器时，VM 将根据本文中列出的要求自动进行编译。
- 我们强烈建议你使用 OVA 模板设置配置服务器。 但是，如果你要设置 VMware VM 的灾难恢复并且无法使用 OVA 模板，则可以使用[提供的这些说明](physical-azure-set-up-source.md)部署配置服务器。
- 如果为本地物理计算机到 Azure 的灾难恢复部署配置服务器，请遵循[本文](physical-azure-set-up-source.md)中的说明。 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>后续步骤
设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复。

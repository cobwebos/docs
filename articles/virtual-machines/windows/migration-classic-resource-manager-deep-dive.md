---
title: 经典到 Azure 资源管理器迁移技术深入探讨
description: 对平台支持的从经典部署模型到 Azure 资源管理器的资源迁移进行技术深入探讨。
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ebd67bdf34bce1d90057ca402b4e3be243b7ec6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81866203"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>有关平台支持的从经典部署模型到 Azure Resource Manager 的迁移的技术深入探讨

> [!IMPORTANT]
> 目前，大约有 90% 的 IaaS VM 在使用 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)。 自 2020 年 2 月 28 日起，经典 VM 已弃用，并将于 2023 年 3 月 1 日完全停用。 [详细了解]( https://aka.ms/classicvmretirement)此弃用以及[它对你的影响](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)。

本文深入探讨如何从 Azure 经典部署模型迁移到 Azure Resource Manager 部署模型。 本文介绍资源和功能级别的资源，让用户了解 Azure 平台如何在两种部署模型之间迁移资源。 有关详细信息，请阅读服务公告文章：[平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>后续步骤

* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN 网关从经典部署模型迁移到资源管理器部署模型](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [将 ExpressRoute 线路和关联的虚拟网络从经典部署模型迁移到资源管理器部署模型](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

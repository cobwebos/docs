---
title: Windows 虚拟桌面用户连接延迟 - Azure
description: Windows 虚拟桌面用户的连接延迟。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128189"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>确定 Windows 虚拟桌面中的用户连接延迟

Windows 虚拟桌面在全球可用。 管理员可以在所需的任何 Azure 区域中创建虚拟机 （VM）。 连接延迟因用户和虚拟机的位置而异。 Windows 虚拟桌面服务将不断推广到新的地理位置，以提高延迟。 
 
[Windows 虚拟桌面体验估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)可帮助您确定最佳位置以优化 VM 的延迟。 我们建议您每两到三个月使用该工具，以确保最佳位置在 Windows 虚拟桌面滚到新区域时未更改。 

## <a name="azure-traffic-manager"></a>Azure 流量管理器

Windows 虚拟桌面使用 Azure 流量管理器，该管理器检查用户的 DNS 服务器的位置以查找最近的 Windows 虚拟桌面服务实例。 我们建议管理员在选择 VM 的位置之前查看用户的 DNS 服务器的位置。

## <a name="next-steps"></a>后续步骤

- 要检查最佳位置以获得最佳延迟，请参阅[Windows 虚拟桌面体验估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)。
- 有关定价计划，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。
- 要开始使用 Windows 虚拟桌面部署，请查看[我们的教程](tenant-setup-azure-active-directory.md)。
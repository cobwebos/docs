---
title: Windows 虚拟桌面用户连接延迟-Azure
description: Windows 虚拟桌面用户的连接延迟。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 403cf584c79bc0a166054ae36c9d2ea50e4b9d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008723"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>确定 Windows 虚拟桌面中的用户连接延迟

Windows 虚拟桌面全局可用。 管理员可以在所需的任何 Azure 区域中 (Vm) 创建虚拟机。 连接延迟将因用户和虚拟机的位置而异。 Windows 虚拟桌面服务将持续推出新的地理区域以提高延迟。

[Windows 虚拟桌面体验估计器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)可帮助你确定优化 vm 延迟的最佳位置。 建议每隔两至三个月使用该工具，以确保在 Windows 虚拟桌面推出到新区域时，最佳位置没有发生变化。

## <a name="azure-traffic-manager"></a>Azure 流量管理器

Windows 虚拟桌面使用 Azure 流量管理器，该管理器检查用户的 DNS 服务器的位置以查找最近的 Windows 虚拟桌面服务实例。 建议管理员在为 Vm 选择位置之前查看用户的 DNS 服务器的位置。

## <a name="next-steps"></a>后续步骤

- 若要查看最佳延迟位置，请参阅 [Windows 虚拟桌面体验估计器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)。
- 有关定价计划，请参阅 [Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。
- 若要开始进行 Windows 虚拟桌面部署，请查看 [我们的教程](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。
---
title: Azure Stack 服务策略 | Microsoft Docs
description: 了解 Azure Stack 服务策略，以及如何使集成系统保持在受支持的状态。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: f495ca12e7cdb1bf61f09bd2d4a8a21654745d8a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服务策略
本文介绍 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态。 

## <a name="update-package-types"></a>更新包类型

集成系统有两种类型的的更新包：Microsoft 软件更新，以及特定于原始设备制造商 (OEM) 硬件供应商的更新，例如驱动程序和固件。 这些更新是以单独的 Azure Stack 更新包提供，而且独立管理。

- **Microsoft 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 功能更新。 可以直接从 Microsoft 下载这些更新包。
- **OEM 硬件供应商提供的更新**。 Azure Stack 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。

## <a name="update-package-release-cadence"></a>更新包发布频率

Microsoft 预期每月发布软件更新包。 但是，可能一个月内发布多个更新或没有任何更新。 OEM 硬件供应商会根据需要发布更新。

Microsoft 更新包具有以下命名约定，可帮助你轻松识别发布日期：

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

例如，2017 年 6 月 15 日发布的 Microsoft 软件更新是版本“1.0.170615.1”。

## <a name="keep-your-system-under-support"></a>保持系统受支持

若要获得系统支持，必须保持在特定时间间隔内更新 Azure Stack。 我们对于 Microsoft 软件更新的延迟拟定的策略是三个月。 如果系统过期超过三个月，会被视为不合规。 必须将系统更新为至少最低支持版本，才能获得支持。 

Microsoft 软件更新包是非累积的，而且先决条件是需要前一个更新包。 如果决定延后一个或多个更新，如果想要获取最新版本，请考虑整体运行时。

下表显示示例更新包发布、其先决条件，以及要维持支持必要的系统最低支持版本。 该表基于 Azure Stack 集成系统的初始版本（内部版本 1708），第一次更新包发布 (1709) 是在 2017 年 9 月。 

| 最新更新包（*示例*） | 先决条件 | 最低支持版本 |
| -- | -- | -- |
| 1710 | 1709 | 不适用 |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1801 | 1712 |
| 1803 | 1802 | 1801 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新](azure-stack-updates.md)



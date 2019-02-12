---
title: Azure Stack 物理设备审核
description: 了解如何集成 Azure Stack 中的物理设备访问审核
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 02/11/2019
keywords: ''
ms.openlocfilehash: c04ec1213d48179e203a8bef47f304fd23203afe
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098959"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Azure Stack 数据中心集成 - 物理设备审核

Azure Stack 中的所有物理设备（例如基板管理控制器 (BMC) 和网络交换机）都会发出审核日志。 可以将它们集成到整体审核解决方案中。 由于设备因不同的 Azure Stack OEM 硬件供应商而异，所以请联系你的供应商来获取有关审核集成的文档。
以下各部分提供了有关 Azure Stack 中的物理设备审核的一些常规信息。  

## <a name="physical-device-access-auditing"></a>物理设备访问审核

Azure Stack 中的所有物理设备都支持使用 TACACS 或 RADIUS。 支持包括访问基板管理控制器 (BMC) 和网络交换机。

Azure Stack 解决方案在推出时并未内置 RADIUS 或 TACACS。 但是，经验证，这些解决方案支持使用市面上现有的 RADIUS 或 TACACS 解决方案。

对于 RADIUS，只有 MSCHAPv2 经过了验证。 它代表使用 RADIUS 的最安全实现。
请咨询 OEM 硬件供应商，在 Azure Stack 解决方案包含的设备中启用 TACAS 或 RADIUS。

## <a name="syslog-forwarding-for-network-devices"></a>网络设备的 Syslog 转发

Azure Stack 中的所有物理网络设备都支持 syslog 消息。 Azure Stack 解决方案未附带 syslog 服务器。 但是，经验证，这些设备支持将消息发送到市面上现有的 syslog 解决方案。

Syslog 目标地址是针对部署所收集的可选参数，但也可以在部署后添加此参数。 要在网络设备上配置 syslog 转发，请咨询你的 OEM 硬件供应商。

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)

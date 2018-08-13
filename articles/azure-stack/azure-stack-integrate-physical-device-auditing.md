---
title: Azure Stack 物理设备审核
description: 了解如何将集成在 Azure Stack 中的物理设备访问审核
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/01/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 459cdf4e1a70ee02d818dd6abe101e4fc3475b68
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034838"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Azure Stack 数据中心集成-审核物理设备

基板管理控制器 (Bmc) 和网络交换机等 Azure Stack 中的所有物理设备发出审核日志，并且应将它们集成到整体审核解决方案。 由于不同 Azure Stack OEM 硬件供应商提供不同的设备，有关审核集成的文档与供应商联系。 以下部分提供一些物理设备审核 Azure Stack 中的一般信息。  

## <a name="physical-device-access-auditing"></a>物理设备访问审核

Azure Stack 中的所有物理设备都支持使用 TACACS 或 RADIUS。 这包括访问基板管理控制器 (BMC) 和网络交换机。

Azure Stack 解决方案在推出时并未内置 RADIUS 或 TACACS。 但是，经验证，这些解决方案支持使用市面上现有的 RADIUS 或 TACACS 解决方案。

对于 RADIUS，只有 MSCHAPv2 经过了验证。 它代表使用 RADIUS 的最安全实现。
请咨询 OEM 硬件供应商，在 Azure Stack 解决方案包含的设备中启用 TACAS 或 RADIUS。

## <a name="syslog-forwarding-for-network-devices"></a>网络设备的 Syslog 转发

在 Azure Stack 中的所有物理网络设备支持的 syslog 消息。 Azure Stack 解决方案未随附 syslog 服务器。 但是，已验证设备以支持在市场中的消息发送到现有的 syslog 解决方案。

Syslog 目标地址是一个可选参数，收集有关部署，但它还可以添加后期部署。 请咨询 OEM 硬件供应商联系以配置 syslog 转发网络设备上。

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)

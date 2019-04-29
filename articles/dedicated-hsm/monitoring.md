---
title: 监视选项 - Azure 专用 HSM | Microsoft Docs
description: Azure 专用 HSM 监视选项和监视职责的概述
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2bcf1d1e007398cf80839f5e1d0bac78fd80db07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912223"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure 专用 HSM 监视

Azure 专用 HSM 服务提供供单个客户使用的物理设备，由客户对设备进行完全的管理控制并承担管理责任。 提供的设备是 [Gemalto SafeNet Luna 7 HSM 型号 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)。  在客户进行预配之后，Microsoft 将不能进行任何管理性访问，只有一个连接的物理串行端口，承担监视角色。 因此，客户将负责典型的运营活动，包括进行全面的监视和日志分析。
客户将全权负责使用 HSM 的应用程序的一切，同时应该由 Gemalto 提供支持或咨询帮助。 考虑到客户对操作安全机制的责任程度，Microsoft 不可能为此服务提供任何类型的高可用性保证。 客户负责确保其应用程序得到正确的配置，以实现高可用性。 Microsoft 会监视并维护设备运行状况和网络连接性。

## <a name="microsoft-monitoring"></a>Microsoft 监视

使用的 Gemalto SafeNet 设备默认情况下将 SNMP 和串行端口设作监视设备的选项。 Microsoft 已将串行端口连接用作连接到设备的物理方式，以便检索有关设备运行状况的基本遥测数据。 这包括温度和组件（例如电源和风扇）状态之类的项目。
为此，Microsoft 使用在 Gemalto 设备上设置的非管理性“监视”角色。 此角色能够检索遥测数据，但不能以管理任务的形式访问设备，也不能以任何方式查看加密信息。 客户可以放心的是，他们的设备是真的由他们自己管理和使用的，可以放心地进行敏感的加密密钥的存储。 如果有哪个客户对这种用于进行基本的运行状况监视的最小访问权限也不放心，该客户可以选择禁用监视帐户。 这样做的明显结果是，Microsoft 将无法获取任何信息，因此不能提供任何有关设备运行状况问题的前摄性通知。 在这种情况下，客户负责设备的运行状况。
监视功能本身设置为每 10 分钟轮询一次设备，以便获取运行状况数据。 考虑到串行通信容易出错的性质，只有在每小时出现多个负面运行状况指标的情况下，才会发出警报。 发出此警报之后，最终会进行前摄性的客户通信来通知此问题。
根据问题的性质，将会采取适当的行动步骤来减轻影响并确保进行低风险的修正。 例如，电源故障是一种热插拔过程，不会造成篡改事件，因此在出现时造成的影响小，给操作带来的风险极小。 其他过程可能需要将设备归零并取消预配，尽量降低给客户带来的安全风险。 在这种情况下，客户需预配一个备用设备，重新加入高可用性配对，因此会触发设备同步。 在很短的时间内就会恢复正常运行，中断情况极其轻微，安全风险最低。  

## <a name="customer-monitoring"></a>客户监视

专用 HSM 服务的价值主张在于，客户可以获得对设备的控制。考虑到设备是通过云提供的设备，该主张显得特别有意义。 这种控制的结果是，客户有责任监视并管理设备的运行状况。 Gemalto SafeNet 设备提供 SNMP 和 Syslog 实施指南。 建议专用 HSM 服务的客户使用该指南（即使 Microsoft 监视帐户始终处于启用状态）。如果禁用 Microsoft 监视帐户，则应将其视为必须遵循的指南。
客户可以通过任一可用技术来确定问题并致电 Microsoft 支持部门，以便启动相应的补救工作。

## <a name="next-steps"></a>后续步骤

建议在进行任何设备预配和应用程序设计或部署之前，先深刻理解此服务的所有重要概念，例如高可用性和安全性。 更多概念级别的主题：

* [高可用性](high-availability.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [可支持性](supportability.md)

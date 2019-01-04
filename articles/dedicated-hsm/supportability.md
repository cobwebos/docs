---
title: 支持 - Azure 专用 HSM | Microsoft Docs
description: 在不同方案中支持 Azure 专用 HSM 的选项和责任范围
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2ed6a79b8736a1d3b472e31ce643c0d1ee085bbb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085903"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure 专用 HSM 的支持

Azure 专用 HSM 服务提供供单个客户使用的物理设备，由客户对设备进行完全的管理控制并承担管理责任。 提供的设备是 [Gemalto SafeNet Luna 7 HSM 型号 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)。 在客户进行预配之后，Microsoft 没有任何管理访问权限，但可以连接一个充当监视角色的物理串行端口。  由于没有访问权限，Microsoft 不能承担软件级别的持续维护或系统管理责任。 因此，典型的操作活动由客户负责。
客户需要对使用 HSM 的应用程序承担所有责任，如需支持或咨询帮助，应该与 Gemalto 联系。 考虑到客户对操作安全机制的责任程度，Microsoft 不可能为此服务提供任何类型的高可用性保证。 客户负责确保其应用程序得到正确的配置，以实现高可用性。 Microsoft 会监视并维护设备运行状况和网络连接。

## <a name="gemalto-support"></a>Gemalto 支持

使用专用 HSM 服务的客户必须与 Gemalto 签署支持合同。 支持合同中规定，客户直接从 Gemalto 接受指导、支持和服务。 从 Gemalto 获得支持的机制是[客户支持门户](https://supportportal.gemalto.com/csm/)。
Gemalto 将提供所需的任何软件组件（例如客户端访问软件和 SDK）来让客户使用 HSM。 此外，Gemalto 将为配置提供支持，并为使用 SafeNet Luna 7 HSM 的应用程序的设计、开发和部署提供咨询服务。

### <a name="software-components"></a>软件组件

HSM 设备的配置中使用各种软件组件：

* 客户端软件
* SDK 中 IsInRole 中的声明
* 工具

### <a name="guidance"></a>指南

Gemalto 通过[客户支持门户](https://supportportal.gemalto.com/csm/)提供管理和配置指导。 使用有效的客户 ID 登录后，可以下载这些文档。 Gemalto 还提供一系列集成指导，以帮助客户实现不同的方案和软件集成。 有关详细信息，请参阅 [Microsoft 的 Gemalto 合作伙伴站点](https://safenet.gemalto.com/partners/microsoft/)。

### <a name="support"></a>支持

与在专用 HSM 服务中使用 HSM 相关的任何软件级问题都应该直接转递给 Gemalto 支持部门。 以上所列的所有软件组件，以及需要后期预配的任何自定义 HSM 配置问题都将由 Gemalto 解决。 有关详细信息，请参阅 [Gemalto 客户支持门户](https://supportportal.gemalto.com/csm/)。

### <a name="consulting-services"></a>咨询服务

对于使用 HSM 的自定义应用程序的设计、开发和部署，如需任何帮助，请联系 Gemalto 客户代表。

## <a name="microsoft-support"></a>Microsoft 支持

Microsoft 负责确保物理 HSM 设备可访问且处于正常运行状态，可供单个客户专用。 设备的管理由客户负责。 Microsoft 的责任包括：

* 确保为设备提供电源和散热装置
* 保持 HSM 的正常运行状态（例如，中断/修复方案）
* 可通过网络访问设备。

如下所述的问题应向 Microsoft 汇报：

* 组件故障
* 整个设备发生故障
* 网络访问问题
* 预配和取消预配问题

Microsoft 可以通过监视角色（非管理角色）使用物理串行端口来访问设备，以获取基本的运行状况遥测数据。  这样，在出现问题时，Microsoft 便可以向客户提供主动通知，但如果客户选择限制此权限，则 Microsoft 做不到这一点。 

### <a name="provisioning-and-decommissioning"></a>预配和解除

客户获取专用 HSM 服务的已批准注册后，可以创建 HSM 资源（目前可以通过 PowerShell 或命令行接口来创建，但不能使用 Azure 门户）。 资源将经历一个分配过程，该过程将指定区域中的物理设备映射到客户的预定义虚拟网络 (VNet)。 设备在 VNet 中出现后，客户可以访问该设备，并按要求对其进一步进行配置。 客户使用 Gemalto 客户端软件和工具访问其专用 HSM。 在资源创建过程中，Microsoft 将提供支持。 自定义配置过程和其他活动由 Gemalto 提供支持。 （请参阅前面的“Gemalto 支持”）。 客户用完 HSM 后，必须将其重置（或归零），以确保不会保存任何数据。 设备重置过程会删除所有自定义配置和数据。 Microsoft 会解除分配该设备，并将其以原始状态返回到池中。 这意味着，将设备返回到池后，不存在以前的客户活动的证据。 

### <a name="hardware-issues"></a>硬件问题

HSM 设备带有冗余且可更换的电源和风扇装置。 如果在设备通电的情况下拆卸风扇装置，会造成篡改事件。 发生组件故障时，Microsoft 将采用最适当的过程来解决组件级问题，并尽最大努力减少对客户服务可用性造成中断和风险。
如果发生更严重的设备故障，Microsoft 将使用可用池中的全新设备来更换故障设备。 客户只需将新设备加入到现有的高可用性对中，即可让其同步并恢复完全正常运行状态。 将拆除故障设备上的数据保存装置，然后在数据中心现场粉碎该设备。 只会将机箱退回到 Gemalto 进行回收。


### <a name="networking-issues"></a>网络问题

如果客户在访问 HSM 设备时遇到网络问题，应联系 Microsoft 支持部门。 使用 SSH 连接到 HSM 设备可以方便地测试网络访问。 如果连接失败，请联系 Microsoft 支持部门。

## <a name="service-level-expectations-for-support"></a>支持的服务级别期望

有关 Microsoft 支持服务级别，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。
有关 Gemalto 支持服务级别，请参阅 [Gemalto 支持概要](https://azure.microsoft.com/support/plans/)。

## <a name="next-steps"></a>后续步骤

建议在进行设备预配和应用程序设计或部署之前，深刻理解高可用性和安全性等重要概念。

* [部署体系结构](deployment-architecture.md)
* [高可用性](high-availability.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [监视](monitoring.md)
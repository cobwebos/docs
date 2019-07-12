---
title: CloudSimple 服务的安全性
description: 描述的 CloudSimple 服务安全性的共担责任模型
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 325915aae43c905236910382f650730a6daa127a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595327"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple 安全概述

本文概述了安全如何在 Azure 的 VMware 解决方案上实现由 CloudSimple 服务、 基础结构和数据中心。 了解有关数据保护和安全、 网络安全性和漏洞和修补程序的管理方式。

## <a name="shared-responsibility"></a>共担责任

Azure 通过 CloudSimple VMware 解决方案使用共担责任模型，确保安全。 在云中受信任的安全被通过为服务提供程序的客户与 Microsoft 共享的职责。 此矩阵的责任提供更高的安全性，并消除单点故障。

## <a name="azure-infrastructure"></a>Azure 基础结构 

Azure 基础结构安全性注意事项包括的数据中心和设备的位置。

### <a name="datacenter-security"></a>数据中心安全性 

Microsoft 已专门用于设计、 构建和操作支持 Azure 物理设施整个部门。 此团队在维持一流物理安全性方面投入了大量的人力物力。 物理安全性的详细信息，请参阅[Azure 设施、 场地和物理安全性](https://docs.microsoft.com/azure/security/azure-physical-security)。

### <a name="equipment-location"></a>设备位置

运行私有云的裸机硬件设备托管在 Azure 数据中心位置。 基于生物识别的双因素身份验证时需要访问该设备所在的位置 cages。

## <a name="dedicated-hardware"></a>专用的硬件

作为 CloudSimple 服务的一部分，所有 CloudSimple 客户与本地附加的磁盘的物理上独立于其他租户硬件都获取专用的裸机主机。 每个节点上运行与 vSAN ESXi 虚拟机监控程序。 通过客户专用的 VMware、 vCenter 和 NSX 管理节点。 不共享租户之间的硬件提供了一层额外的隔离和安全保护。

## <a name="data-security"></a>数据安全

客户保持控制和其数据的所有权。 数据管理的客户数据是客户的责任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>静态数据和内部网络中的动态数据的数据保护

对于私有云环境中的静态数据，可以使用 vSAN 加密。 vSAN 加密配合虚拟网络或本地中的 VMware 认证外部密钥管理服务器 (KMS)。 你自己控制数据加密密钥。 私有云内的动态数据，vSphere 支持对所有 VMkernel 流量，包括 vMotion 通信线路上的数据加密。

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>数据的所需的公用网络中移动的数据保护

若要保护通过公用网络移动的数据，可以创建 IPsec 和 SSL VPN 隧道的私有云。 支持常见的加密方法，包括 128 位和 256 位 AES。 使用标准的加密机制，例如 SSH、 TLS 1.2 和安全的 RDP 加密传输，其中包括身份验证、 管理访问权限，以及客户数据中的数据。 传输敏感信息的方式的通信使用标准的加密机制。

### <a name="secure-disposal"></a>安全处理 

如果 CloudSimple 服务已到期或终止，您负责移除或删除你的数据。 所以，就会与您要删除或返回的所有客户数据，所不同的范围内 CloudSimple 是适用法律有要求保留个人数据中的客户协议，提供协作 CloudSimple。 如有必要保留任何个人数据，CloudSimple 存档数据，并实现合理的措施来防止客户数据的任何进一步处理。

### <a name="data-location"></a>数据位置

如果设置了私有云时，你选择部署位置的 Azure 区域。 VMware 虚拟机数据不是从该物理数据中心内移动，除非你执行数据迁移或场外备份数据。 此外可以承载的工作负荷和存储多个 Azure 区域中的数据，如果您需要的。

驻留在私有云超聚合节点中的客户数据不会遍历位置而没有租户管理员的显式操作。 它是由您负责实现高可用性的方式工作负荷。

### <a name="data-backups"></a>数据备份
CloudSimple 不备份或存档的客户数据。 CloudSimple 确实会执行 vCenter 和 NSX 数据来提供管理服务器的高可用性的定期的备份。 在备份之前的所有数据进行都加密在 vCenter 源使用 VMware Api。 加密的数据传输并存储在 Azure blob 中。 备份加密密钥存储在高度安全 CloudSimple 托管的保管库在 Azure 中的 CloudSimple 虚拟网络中运行。

## <a name="network-security"></a>网络安全

CloudSimple 解决方案依赖于多个网络安全层。

### <a name="azure-edge-security"></a>Azure 边缘安全

CloudSimple 服务是基于由 Azure 提供的基本网络安全性。 Azure 应用检测和及时响应异常入口或出口流量模式和分布式的拒绝服务 (DDoS) 攻击与关联的基于网络的攻击的深度防御技术。 此安全控件适用于私有云环境和开发的 CloudSimple 控制平面软件。

### <a name="segmentation"></a>分段

CloudSimple 服务具有逻辑上独立地限制对你自己的私有云环境中的专用网络的访问的第 2 层网络。 通过使用防火墙，则可以进一步保护你的私有云的网络。 在 CloudSimple 门户中定义所有网络流量，其中包括内部私有云通信、 间私有云通信、 到 Internet，一般流量和到本地网络流量的东-西和北-南网络流量控制的规则通过 IPsec VPN 或 Azure ExpressRoute 连接。

## <a name="vulnerability-and-patch-management"></a>漏洞和补丁管理 

CloudSimple 负责定期安全修补的受管理的 VMware 软件，ESXi、 vCenter 和 NSX 等。

## <a name="identity-and-access-management"></a>标识和访问管理

客户可以使用进行身份验证到其 Azure 帐户 （在 Azure Active Directory) 多重身份验证或 SSO 为首选。 在 Azure 门户中，可以无需重新输入凭据启动 CloudSimple 门户。

CloudSimple 支持私有云 vCenter 标识源的可选配置。 可以使用[的本地标识源](https://docs.azure.cloudsimple.com/set-vcenter-identity)，为私有云，新的标识源或[Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad)。

默认情况下，客户提供所需的私有云内的 vCenter 的日常操作的权限。 此权限级别不包括对 vCenter 管理访问权限。 如果暂时需要管理访问权限，你可以[提升你的特权](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)在有限期限内完成管理任务时。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)。
* 了解如何[创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)。
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)。

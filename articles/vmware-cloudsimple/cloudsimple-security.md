---
title: Azure VMware 解决方案（按云简单 ） - 云简单服务的安全性
description: 描述云简单服务安全性的共享责任模型
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024987"
---
# <a name="cloudsimple-security-overview"></a>云简单安全概述

本文概述了如何通过云简单服务、基础结构和数据中心在 Azure VMware 解决方案上实现安全性。 了解数据保护和安全、网络安全以及如何管理漏洞和修补程序。

## <a name="shared-responsibility"></a>共担责任

云简单 Azure VMware 解决方案使用共享责任模型进行安全性保护。 云中值得信赖的安全性是通过客户和 Microsoft 作为服务提供商的共同责任实现的。 这种责任矩阵提供了更高的安全性，并消除了单点故障。

## <a name="azure-infrastructure"></a>Azure 基础结构

Azure 基础结构安全注意事项包括数据中心和设备位置。

### <a name="datacenter-security"></a>数据中心安全性

Microsoft 的整个部门都致力于设计、构建和操作支持 Azure 的物理设施。 此团队在维持一流物理安全性方面投入了大量的人力物力。 有关物理安全的详细信息，请参阅[Azure 设施、场所和物理安全](../security/azure-physical-security.md)。

### <a name="equipment-location"></a>设备位置

运行私有云的裸机硬件设备托管在 Azure 数据中心位置。  该设备所在的笼子需要基于生物识别的双因素认证才能获得访问。

## <a name="dedicated-hardware"></a>专用硬件

作为 CloudSimple 服务的一部分，所有 CloudSimple 客户都获得专用裸机主机，这些主机具有本地连接的磁盘，这些磁盘与其他租户硬件物理隔离。 具有 vSAN 的 ESXi 虚拟机管理程序在每个节点上运行。 节点通过客户专用 VMware vCenter 和 NSX 进行管理。 租户之间不共享硬件可提供额外的隔离和安全保护层。

## <a name="data-security"></a>数据安全

客户保持对其数据的控制和所有权。 客户数据的数据管理是客户的责任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>内部网络中静止数据和动态数据的数据保护

对于私有云环境中静态的数据，可以使用 vSAN 加密。 vSAN 加密适用于您自己的虚拟网络或本地的 VMware 认证外部密钥管理服务器 （KMS）。  您可以自己控制数据加密密钥。 对于私有云中正在启动的数据，vSphere 支持对所有 vmkernel 流量（包括 vMotion 流量）通过线路加密数据。

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>数据数据保护，用于在公共网络中移动所需的数据

为了保护通过公共网络传输的数据，可以为私有云创建 IPsec 和 SSL VPN 隧道。 支持通用加密方法，包括 128 字节和 256 字节 AES。 传输中的数据（包括身份验证、管理访问和客户数据）通过标准加密机制（SSH、TLS 1.2 和安全 RDP）进行加密。 传输敏感信息的通信使用标准加密机制。

### <a name="secure-disposal"></a>安全处置

如果您的 CloudSimple 服务过期或终止，您有责任删除或删除您的数据。 CloudSimple 将与您合作，删除或退回客户协议中提供的所有客户数据，除非适用法律要求 CloudSimple 保留部分或全部个人数据。 如果需要保留任何个人数据，CloudSimple 将存档数据并实施合理措施，以防止客户数据进一步处理。

### <a name="data-location"></a>数据位置

设置私有云时，请选择将部署云的 Azure 区域。 除非您执行数据迁移或异地数据备份，否则 VMware 虚拟机数据不会从该物理数据中心移动。 如果需要，还可以在多个 Azure 区域中托管工作负荷和将数据存储。

如果没有租户管理员的显式操作，驻留在私有云超融合节点的客户数据不会遍历位置。 您有责任以高度可用的方式实现工作负载。

### <a name="data-backups"></a>数据备份

云简单不会备份或存档客户数据。 CloudSimple 会定期备份 vCenter 和 NSX 数据，以提供高可用性的管理服务器。 在备份之前，所有数据都使用 VMware API 在 vCenter 源进行加密。 加密数据在 Azure Blob 中传输和存储。 备份的加密密钥存储在 Azure 中的 CloudSimple 虚拟网络中运行的高度安全的 CloudSimple 托管保管库中。

## <a name="network-security"></a>网络安全

CloudSimple 解决方案依赖于网络安全层。

### <a name="azure-edge-security"></a>Azure 边缘安全性

CloudSimple 服务构建在 Azure 提供的基本网络安全之上。 Azure 应用纵深防御技术来检测并及时响应与异常入侵或出口流量模式和分布式拒绝服务 （DDoS） 攻击相关的基于网络的攻击。 此安全控制适用于私有云环境和 CloudSimple 开发的控制平面软件。

### <a name="segmentation"></a>分段

CloudSimple 服务具有逻辑上独立的第 2 层网络，这些网络限制对私有云环境中您自己的专用网络的访问。 您可以使用防火墙进一步保护您的私有云网络。 CloudSimple 门户允许您为所有网络流量定义 EW 和 NS 网络流量控制规则，包括私有云内部流量、私有云间流量、Internet 的普通流量以及通过 IPsec VPN 或快速路由连接。

## <a name="vulnerability-and-patch-management"></a>漏洞和修补程序管理

CloudSimple 负责定期对托管 VMware 软件（ESXi、vCenter 和 NSX）进行安全修补。

## <a name="identity-and-access-management"></a>标识和访问管理

客户可以使用多重身份验证或首选 SSO 对其 Azure 帐户（Azure AD 中）进行身份验证。 在 Azure 门户中，无需重新输入凭据即可启动 CloudSimple 门户。

CloudSimple 支持私有云 vCenter 标识源的可选配置。 可以使用[本地标识源](set-vcenter-identity.md)、私有云的新标识源或[Azure AD](azure-ad.md)。

默认情况下，客户将获得在私有云中进行 vCenter 日常操作所需的权限。 此权限级别不包括对 vCenter 的管理访问权限。 如果临时需要管理访问权限，则可以在完成管理任务时在有限时间内[升级权限](escalate-private-cloud-privileges.md)。

---
title: Azure VMware 解决方案（按云简单 - 安全私有云）
description: 描述如何通过云简单私有云保护 Azure VMware 解决方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565972"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>如何保护您的私有云环境

定义 Azure 云简单服务、云简单门户和来自 Azure 的私有云的基于角色的访问控制 （RBAC）。  使用 VMware SSO 指定用于访问私有云 vCenter 的用户、组和角色。  

## <a name="rbac-for-cloudsimple-service"></a>用于云简单服务的 RBAC

创建云简单服务需要在 Azure 订阅上**担任所有者**或**参与者**角色。  默认情况下，所有所有者和参与者都可以创建云简单服务并访问 CloudSimple 门户以创建和管理私有云。  每个区域只能创建一个 CloudSimple 服务。  要限制对特定管理员的访问，请按照以下步骤操作。

1. 在 Azure 门户上的新**资源组中**创建云简单服务
2. 为资源组指定 RBAC。
3. 购买节点并使用与 CloudSimple 服务相同的资源组

只有资源组中具有**所有者**或**参与者**权限的用户才能看到云简单服务并启动云简单门户。

有关 RBAC 的详细信息，请参阅[什么是 Azure 资源的基于角色的访问控制 （RBAC）。](../role-based-access-control/overview.md)

## <a name="rbac-for-private-cloud-vcenter"></a>用于私有云 vCenter 的 RBAC

创建私有云`CloudOwner@cloudsimple.local`时，在 vCenter SSO 域中创建默认用户。  云所有者用户具有管理 vCenter 的权限。 其他标识源将添加到 vCenter SSO 中，以便向不同的用户授予访问权限。  预定义的角色和组设置在 vCenter 上，可用于添加其他用户。

### <a name="add-new-users-to-vcenter"></a>将新用户添加到 vCenter

1. [升级](escalate-private-cloud-privileges.md)**云所有者\@云简单.** 私有云上的本地用户的权限。
2. 使用**云所有者\@云简单**登录 vCenter. 本地
3. [添加 vCenter 单登录用户](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)。
4. 将用户添加到[vCenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

有关预定义角色和组的详细信息，请参阅[VMware vCenter 的云简单私有云权限模型](learn-private-cloud-permissions.md)一文。

### <a name="add-new-identity-sources"></a>添加新标识源

您可以为私有云的 vCenter SSO 域添加其他标识提供程序。  标识提供程序提供身份验证，vCenter SSO 组为用户提供授权。

* 在私有云 vCenter 上[使用活动目录作为标识提供程序](set-vcenter-identity.md)。
* 在私有云 vCenter 上[使用 Azure AD 作为标识提供程序](azure-ad.md)

1. [升级](escalate-private-cloud-privileges.md)**云所有者\@云简单.** 私有云上的本地用户的权限。
2. 使用**云所有者\@云简单**登录 vCenter. 本地
3. 将用户从标识提供程序添加到[vCenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

## <a name="secure-network-on-your-private-cloud-environment"></a>在私有云环境中保护网络

私有云环境的网络安全是通过保护网络访问和控制资源之间的网络流量来控制的。

### <a name="access-to-private-cloud-resources"></a>访问私有云资源

私有云 vCenter 和资源访问通过安全网络连接进行：

* **[快速路由连接](on-premises-connection.md)**。 ExpressRoute 提供来自本地环境的安全、高带宽、低延迟连接。  使用连接可以允许您的本地服务、网络和用户访问您的私有云 vCenter。
* **[站点到站点 VPN 网关](vpn-gateway.md)**。 站点到站点 VPN 通过安全隧道从本地访问您的私有云资源。  您可以指定哪些本地网络可以向私有云发送和接收网络流量。
* **[点对点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**。 使用点对点 VPN 连接快速远程访问私有云 vCenter。

### <a name="control-network-traffic-in-private-cloud"></a>控制私有云中的网络流量

防火墙表和规则控制私有云中的网络流量。  防火墙表允许您根据表中定义的规则的组合控制源网络或 IP 地址与目标网络或 IP 地址之间的网络流量。

1. 创建[防火墙表](firewall.md#add-a-new-firewall-table)。
2. [将规则添加到](firewall.md#create-a-firewall-rule)防火墙表。
3. [将防火墙表附加到 VLAN/子网](firewall.md#attach-vlans-subnet)。

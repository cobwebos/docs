---
title: 适用于网络的 Azure 安全中心建议
description: 本文列出了 Azure 安全中心的安全建议，这些建议可帮助保护你的网络资源。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781964"
---
# <a name="network-recommendations---reference-guide"></a>网络建议-参考指南

本文提供了有关网络拓扑和面向 internet 的终结点的 Azure 安全中心中可能会看到的建议的完整列表。

有关如何查找这些内容以及如何解决这些问题的说明，请参阅[此处](security-center-network-recommendations.md)。

## <a name="network-recommendations"></a>网络建议

|建议名称|描述|Severity|安全评分|资源类型|
|----|----|----|----|----|----|
|应启用子网级别的网络安全组|启用网络安全组来控制子网中部署的资源的网络访问。|高/中|30|子网|
|虚拟机应与网络安全组相关联|启用网络安全组以控制虚拟机的网络访问。|高/中|30|虚拟机|
|对于具有面向 Internet 的 Vm 的许可网络安全组，应限制访问权限|通过限制现有允许规则的访问权限，强化面向 Internet 的 Vm 的网络安全组。|高|20|虚拟机|
|应强化 IaaS Nsg 上的 web 应用程序的规则|强化运行 web 应用程序的虚拟机的网络安全组（NSG），其中包含对 web 应用程序端口而言过于宽松的 NSG 规则。|高|20|虚拟机|
|应限制对应用服务的访问|通过更改网络配置来限制对应用服务的访问，拒绝范围太大的入站流量。|高|10|应用服务|
|应在虚拟机上关闭管理端口|强化虚拟机的网络安全组，以限制对管理端口的访问。|高|10|虚拟机|
应启用 DDoS 防护标准版|启用 DDoS 保护服务标准，保护包含具有公共 Ip 的应用程序的虚拟网络。 DDoS 保护支持网络容量耗尽和协议攻击的缓解。|高|10|虚拟网络|
|应禁用虚拟机上的 IP 转发|禁用 IP 转发。 在虚拟机的 NIC 上启用 IP 转发后，计算机可以接收发送到其他目标的流量。 很少需要 IP 转发（例如，使用 VM 作为网络虚拟设备时），因此应由网络安全团队进行审核。|中型|10|虚拟机|
|只能通过 HTTPS 访问 Web 应用程序|为 web 应用程序启用 "仅 HTTPS" 访问权限。 使用 HTTPS 可确保服务器/服务进行身份验证，并保护传输中的数据免受网络层窃听攻击。|中型|20|Web 应用程序|
|应在虚拟机上应用实时网络访问控制|应用实时（JIT）虚拟机（VM）访问控制以永久锁定对所选端口的访问权限，并使授权用户仅在有限的时间内通过 JIT 打开这些端口。|高|20|虚拟机|
|只能通过 HTTPS 访问函数应用|为 function app 启用 "仅 HTTPS" 访问权限。 使用 HTTPS 可确保服务器/服务进行身份验证，并保护传输中的数据免受网络层窃听攻击。|中型|20|函数应用|
|应该启用安全传输到存储帐户|启用到存储帐户的安全传输。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器与服务之间的身份验证，并保护数据免遭网络层攻击（如中间人、窃听和会话劫持）的传输。|高|20|存储帐户|


## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [监视标识和访问](security-center-identity-access.md)
* [保护您的计算机和应用程序](security-center-virtual-machine-protection.md)
* [保护 Azure SQL 服务](security-center-sql-service-recommendations.md)


---
title: "规划 Azure 堆栈的 azure 堆栈防火墙集成系统 |Microsoft 文档"
description: "描述 Azure 堆栈 Azure 连接的多节点部署的 Azure 堆栈防火墙注意事项。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 919618c0779d47f0add02d5e7d3ab9ab4b5bdd10
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-firewall-integration"></a>Azure 堆栈防火墙集成
建议你使用防火墙设备来帮助安全 Azure 堆栈。 虽然防火墙可以帮助与分布式的拒绝服务 (DDOS) 攻击、 入侵检测等内容检查内容，但它们也可能会变为如 blob、 表和队列的 Azure 存储服务的形成吞吐量瓶颈。

根据 Azure Active Directory (Azure AD) 或 Windows Server Active Directory 联合身份验证服务 (AD FS) 的标识模型，你可能需要发布的 AD FS 终结点。 如果使用断开连接的部署模式时，必须发布的 AD FS 终结点。 有关详细信息，请参阅[数据中心集成标识文章](azure-stack-integrate-identity.md)。

Azure 资源管理器 （管理员）、 管理员门户和密钥保管库 （管理员） 终结点一定不需要外部发布。 例如，作为服务提供商，你可能想要限制的受攻击面并仅在您的网络，而不是从 internet 管理从 Azure 堆栈。

对于企业组织外部的网络可以是现有的企业网络。 在此类方案中，你必须将这些终结点，若要从企业网络中运行 Azure 堆栈。

### <a name="network-address-translation"></a>网络地址转换
网络地址转换 (NAT) 是推荐的方法，以允许部署虚拟机 (DVM) 以访问外部资源和在部署过程中 internet 以及紧急情况下恢复控制台 (ERCS) Vm 或特权终结点 (PEP) 期间注册和故障排除。

NAT 也可在外部网络或公共 Vip 上的公共 IP 地址的替代方法。 但是，不建议这样做，因为它限制租户用户体验与增加复杂性。 两个选项是 1:1 NAT 这仍然需要每个用户 IP 池或多： 1 NAT 这要求每个包含用户可能使用的所有端口关联的用户 VIP 的 NAT 规则上的一个公共 IP。

下面是一些公共 vip 使用 NAT 的不足之处：
- 由于用户控制其自己的终结点和软件定义网络 (SDN) 堆栈中的其自己发布规则管理防火墙规则时，NAT 会增加开销。 用户必须联系 Azure 堆栈运算符获取发布，其 Vip 和更新的端口列表。
- 虽然 NAT 使用情况限制的用户体验，它使能够完全控制运算符对发布请求。
- 对于使用 Azure 的混合云方案，请考虑 Azure 不支持设置使用 VPN 隧道连接到终结点使用 nat。

### <a name="ssl-decryption"></a>SSL 解密
当前建议禁用 Azure 堆栈的所有流量的 SSL 解密。 如果将来的更新在支持它，则指南将提供有关如何启用 Azure 堆栈的 SSL 解密。

## <a name="edge-firewall-scenario"></a>边缘防火墙方案
在边缘部署中，直接在边缘路由器或防火墙后面部署 Azure 堆栈。 在这些情况下，它被支持对防火墙后，才更高版本边框或充当边框设备，如果它支持相等成本多路径 (ECMP) 与 BGP 或静态路由。

通常情况下，公共路由的 IP 地址从外部网络的公共 VIP 池指定在部署时。 在边缘方案中，建议不要使用任何其他网络上的路由的公共 Ip，出于安全目的。 这种情况下使用户可以体验如下所示如 Azure 公有云的完整自受控的云体验。  

![Azure 堆栈边缘防火墙示例](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>企业 intranet 或外围网络防火墙方案
在企业 intranet 或外围部署中，Azure 堆栈部署在多分区的防火墙或边缘防火墙和内部企业网络防火墙之间。 其流量会分发之间的安全，外围网络 （或外围网络），并为不安全区域如下所述：

- **安全区域**： 这是使用内部或企业路由的 IP 地址的内部网络。 安全网络设置可划分、 在防火墙上，具有可通过 NAT 的 internet 出站访问和通常可从访问你通过内部网络的数据中心内的任何地方。 所有 Azure 堆栈网络应都位于除外的外部网络的公共 VIP 池的安全区域。
- **外围区域**。 外围网络是外部或面向应用程序，如 Web 服务器通常部署 internet。 它通常将监视防火墙以避免如 DDoS 和入侵 （攻击），同时仍允许来自 internet 的指定的入站的流量的攻击。 仅外部网络公共 VIP 池 Azure 堆栈的应位于外围网络区域。
- **不安全区域**。 这是外部的网络中，internet。 它**不**建议部署 Azure 堆栈不安全区域中。

![Azure 堆栈外围网络示例](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>了解详细信息
详细了解[使用由 Azure 堆栈的终结点端口和协议](azure-stack-integrate-endpoints.md)。

## <a name="next-steps"></a>后续步骤
[Azure 堆栈 PKI 要求](azure-stack-pki-certs.md)


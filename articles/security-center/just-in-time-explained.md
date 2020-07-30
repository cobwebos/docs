---
title: 了解 Azure 安全中心的实时虚拟机访问
description: 本文档介绍 Azure 安全中心中的实时 VM 访问如何帮助你控制对 Azure 虚拟机的访问
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: dfdb717a27af8dc7f3186ac7afdff4d1eb3d79f5
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420832"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>了解实时（JIT） VM 访问

本页介绍 Azure 安全中心的实时（JIT） VM 访问功能和建议背后的逻辑的原理。

若要了解如何使用 Azure 门户（安全中心或 Azure 虚拟机）或以编程方式将 JIT 应用于 Vm，请参阅[如何使用 JIT 保护管理端口](security-center-just-in-time.md)。


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>在虚拟机上打开管理端口的风险

威胁参与者主动搜寻可访问的计算机和打开的管理端口（如 RDP 或 SSH）。 所有虚拟机都是攻击的潜在目标。 VM 成功泄露后，将用作攻击环境中进一步资源的入口点。



## <a name="why-jit-vm-access-is-the-solution"></a>为什么使用 JIT VM 访问解决方案 

与所有网络安全防护技术一样，你的目标应该是减小攻击面。 在这种情况下，这意味着打开的端口更少，特别是管理端口。

你的合法用户也使用这些端口，因此将其保持关闭状态是不切实际的。

为了解决此难题，Azure 安全中心提供了 JIT。 使用 JIT，你可以将入站流量锁定到 Vm，降低遭受攻击的可能性，同时提供在需要时轻松连接到 Vm 的访问权限。



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>JIT 如何与网络安全组和 Azure 防火墙一起操作

启用实时 VM 访问时，可以选择要阻止入站流量的 VM 上的端口。 安全中心针对[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)（NSG）中的选定端口和[Azure 防火墙规则](https://docs.microsoft.com/azure/firewall/rule-processing)确保 "拒绝所有入站流量" 规则。 这些规则限制对 Azure Vm 管理端口的访问，并防止其受到攻击。 

如果所选端口的其他规则已经存在，则这些现有规则优先于新的 "拒绝所有入站流量" 规则。 如果所选端口上没有现有规则，则新规则将在 NSG 和 Azure 防火墙中取得最高优先级。

当用户请求访问 VM 时，安全中心会检查用户是否具有该 VM 的[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 权限。 如果批准了请求，安全中心会将 Nsg 和 Azure 防火墙配置为允许从相关 IP 地址（或范围）到所选端口的入站流量。 在该时间到期后，安全中心会将 NSG 还原为以前的状态。 已建立的连接不会中断。

> [!NOTE]
> JIT 不支持由[Azure 防火墙管理器](https://docs.microsoft.com/azure/firewall-manager/overview)控制的 azure 防火墙所保护的虚拟机。




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>安全中心如何确定哪些 Vm 应应用 JIT

下图显示了在决定如何分类受支持的 Vm 时，安全中心适用的逻辑： 

[![实时（JIT）虚拟机（VM）逻辑流](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

当安全中心发现可以利用 JIT 的计算机时，它会将该计算机添加到建议的不**正常资源**选项卡。 

![实时（JIT）虚拟机（VM）访问建议](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>常见问题解答-有关实时虚拟机访问的问题

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>配置和使用 JIT 需要哪些权限？

如果要创建可用于 JIT 的自定义角色，则需要下表中的详细信息。

> [!TIP]
> 若要为需要请求对 VM 的 JIT 访问权限的用户创建最小特权角色，而不执行其他任何 JIT 操作，请使用安全中心 GitHub 社区页面中的[JitLeastPrivilegedRole 脚本](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)。

| 使用户能够： | 要设置的权限|
| --- | --- |
| 配置或编辑 VM 的 JIT 策略 | *将这些“操作”分配给角色：*  <ul><li>在与 VM 关联的订阅或资源组的作用域中：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在虚拟机的订阅或资源组的作用域中： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|请求 JIT 对 VM 的访问权限 | *将这些“操作”分配给用户：*  <ul><li>在与 VM 关联的订阅或资源组的作用域中：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在与 VM 关联的订阅或资源组的作用域中：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在订阅或资源组或 VM 的作用域中：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在订阅或资源组或 VM 的作用域中：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|读取 JIT 策略| *将这些“操作”分配给用户：*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>后续步骤

此页说明了应使用实时（JIT）虚拟机（VM）访问的_原因_。 

转到操作指南文章，了解如何启用 JIT 并请求访问启用 JIT 的 Vm：

> [!div class="nextstepaction"]
> [如何通过 JIT 保护管理端口](security-center-just-in-time.md)

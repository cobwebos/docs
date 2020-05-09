---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: 78bf29a170152666d82ec26504ee8f61ed90636a
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615984"
---
## <a name="attack-scenario"></a>攻击方案

暴力攻击通常以攻击管理端口为手段来获取对 VM 的访问权限。 如果成功，则攻击者可以获得对 VM 的控制权并建立通向你的环境的据点。

降低遭受暴力攻击的可能性的一种方法是限制端口处于打开状态的时间量。 管理端口不需要始终处于打开状态。 它们只需要在连接到 VM 时打开，例如执行管理或维护任务。 如果启用了实时功能，安全中心会使用[网络安全组](../articles/virtual-network/security-overview.md#security-rules) (NSG) 和 Azure 防火墙规则，这些规则将限制对管理端口的访问以使其不会成为攻击者的目标。

![实时方案](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 如何访问工作？

如果启用了实时访问，安全中心会通过创建 NSG 规则来锁定发往 Azure VM 的入站流量。 你需要选择要锁定 VM 上的哪些端口的入站流量。 这些端口将受实时解决方案控制。

当用户请求访问 VM 时，安全中心会检查用户是否具有该 VM 的[基于角色的访问控制 (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) 权限。 如果请求获得批准，安全中心将自动配置网络安全组 (NSG) 和 Azure 防火墙，以便在指定的时间内允许入站流量到达选定端口和请求的源 IP 地址或范围。 在该时间到期后，安全中心会将 NSG 还原为以前的状态。 但是，那些已经建立的连接不会中断。

 > [!NOTE]
 > 如果对 Azure 防火墙后面的 VM 批准了 JIT 访问请求，则安全中心会自动更改 NSG 和防火墙策略规则。 在指定的时间内，规则将允许将入站流量发送到选定的端口以及请求的源 IP 地址或范围。 此时间过后，安全中心会将防火墙和 NSG 规则还原到其以前的状态。


## <a name="roles-that-can-read-jit-policies"></a>可读取 JIT 策略的角色

**读取**者和**SecurityReader**角色都可以读取策略。

## <a name="permissions-needed-to-configure-and-use-jit"></a>配置和使用 JIT 所需的权限

如果要创建可用于 JIT 的自定义角色，你将需要以下详细信息：

| 使用户能够： | 要设置的权限|
| --- | --- |
| 配置或编辑 VM 的 JIT 策略 | *将这些“操作”分配给角色：*  <ul><li>在与 VM 关联的订阅或资源组的作用域中：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在虚拟机的订阅或资源组的作用域中： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|请求 JIT 对 VM 的访问权限 | *将这些“操作”分配给用户：*  <ul><li>在与 VM 关联的订阅或资源组的作用域中：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在与 VM 关联的订阅或资源组的作用域中：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在订阅或资源组或 VM 的作用域中：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在订阅或资源组或 VM 的作用域中：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|读取 JIT 策略| *将这些“操作”分配给用户：*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
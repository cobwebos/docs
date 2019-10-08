---
title: Azure 安全中心中的实时虚拟机访问 | Microsoft Docs
description: 本文档说明 Azure 安全中心中的实时 VM 访问如何帮助你控制对 Azure 虚拟机的访问。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: a6326b2ea9b4c2247df4f93eba904b7527666131
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996390"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>使用实时功能管理虚拟机访问

实时（JIT）虚拟机（VM）访问可用于锁定到 Azure Vm 的入站流量，降低攻击的风险，同时提供在需要时轻松连接到 Vm 的权限。

> [!NOTE]
> 实时功能在安全中心的标准层上可用。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。


> [!NOTE]
> 安全中心实时 VM 访问当前仅支持通过 Azure 资源管理器部署的 VM。 若要了解有关经典部署模型和资源管理器部署模型的详细信息，请参阅 [Azure 资源管理器与经典部署](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="attack-scenario"></a>攻击方案

暴力攻击通常以攻击管理端口为手段来获取对 VM 的访问权限。 如果成功，则攻击者可以获得对 VM 的控制权并建立通向你的环境的据点。

降低遭受暴力攻击的可能性的一种方法是限制端口处于打开状态的时间量。 管理端口不需要始终打开。 只需在连接到 VM 时打开这些设备，例如执行管理或维护任务。 如果启用了实时功能，安全中心会使用[网络安全组](../virtual-network/security-overview.md#security-rules) (NSG) 和 Azure 防火墙规则，这些规则将限制对管理端口的访问以使其不会成为攻击者的目标。

![实时方案](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 如何访问工作？

如果启用了实时访问，安全中心会通过创建 NSG 规则来锁定发往 Azure VM 的入站流量。 你需要选择要锁定 VM 上的哪些端口的入站流量。 这些端口将受实时解决方案控制。

当用户请求访问 VM 时，安全中心会检查该用户是否具有该 VM 的[基于角色的访问控制（RBAC）](../role-based-access-control/role-assignments-portal.md)权限。 如果请求获得批准，安全中心将自动配置网络安全组 (NSG) 和 Azure 防火墙，以便在指定的时间内允许入站流量到达选定端口和请求的源 IP 地址或范围。 在该时间到期后，安全中心会将 NSG 还原为以前的状态。 但是，那些已经建立的连接不会中断。

 > [!NOTE]
 > 如果对 Azure 防火墙后面的 VM 批准了 JIT 访问请求，则安全中心会自动更改 NSG 和防火墙策略规则。 在指定的时间内，规则将允许将入站流量发送到选定的端口以及请求的源 IP 地址或范围。 此时间过后，安全中心会将防火墙和 NSG 规则还原到其以前的状态。


## <a name="permissions-needed-to-configure-and-use-jit"></a>配置和使用 JIT 所需的权限

| 使用户能够： | 要设置的权限|
| --- | --- |
| 配置或编辑 VM 的 JIT 策略 | 将这些操作分配到角色：  <ul><li>与 VM 关联的订阅或资源组的作用域：<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> 在订阅、资源组或 VM 的作用域： <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|请求 JIT 对 VM 的访问权限 | 将这些操作分配到用户：  <ul><li>与 VM 关联的订阅或资源组的作用域：<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  在订阅、资源组或 VM 的作用域：<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>在 VM 上配置 JIT

有三种方法可在 VM 上配置 JIT 策略：

- [在 Azure 安全中心配置 JIT 访问](#jit-asc)
- [在 Azure VM 边栏选项卡中配置 JIT 访问](#jit-vm)
- [以编程方式在 VM 上配置 JIT 策略](#jit-program)

## <a name="configure-jit-in-asc"></a>在 ASC 中配置 JIT

在 ASC 中，可以配置 JIT 策略并使用 JIT 策略请求访问 VM


### 在 ASC 中配置对 VM 的 JIT 访问<a name="jit-asc"></a>

1. 打开“安全中心”仪表板。

2. 在左侧窗格中，选择“实时 VM 访问”。

    ![实时 VM 访问磁贴](./media/security-center-just-in-time/just-in-time.png)

    “实时 VM 访问”窗口随即打开。

      ![启用实时访问](./media/security-center-just-in-time/enable-just-in-time.png)

    “实时 VM 访问”提供 VM 的状态信息：

    - **已配置** - 已配置为支持实时 VM 访问的 VM。 提供的数据是针对过去一周的，并且针对每个 VM 包括了已批准的请求数、上次访问日期和时间以及上一个用户。
    - **建议**的 vm-可支持实时 VM 访问但尚未配置为的 vm。 建议为这些 VM 启用实时 VM 访问控制。
    - **不推荐** - 导致不推荐某个 VM 的可能原因有：
      - 缺少 NSG - 实时解决方案需要 NSG 准备就绪。
      - 经典 VM - 安全中心实时 VM 访问当前仅支持通过 Azure 资源管理器部署的 VM。 实时解决方案不支持经典部署。 
      - 其他 - 如果在订阅或资源组的安全策略中未开启实时解决方案，或者 VM 缺少公共 IP 且没有已准备就绪的 NSG，则该 VM 将位于此类别中。

3. 选择“建议”选项卡。

4. 在“虚拟机”下，单击要启用的 VM。 这会在 VM 旁边放置一个复选标记。

5. 单击“在 VM 上启用 JIT”。
   -. 此边栏选项卡显示通过 Azure 安全中心建议的默认端口：
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. 此外还可以配置自定义端口：

      1. 单击“添加”。 此时会打开“添加端口配置”窗口。
      2. 对于选择配置的每个端口，无论是默认端口还是自定义端口，都可以自定义下列设置：

    - **协议类型** - 批准某个请求时，此端口允许的协议。
    - **允许的源 IP 地址** - 批准某个请求时，此端口允许的 IP 范围。
    - **最大请求时间** - 可以打开特定端口的最大时间范围。

     3. 单击 **“确定”** 。

1. 单击“保存”。

> [!NOTE]
>如果为 VM 启用 JIT VM 访问，Azure 安全中心将在与所选端口关联的网络安全组和 Azure 防火墙中为该端口创建“拒绝所有入站流量”规则。 如果为所选端口创建了其他规则，则现有规则优先于新的 "拒绝所有入站流量" 规则。 如果所选端口没有现有的规则，则新的“拒绝所有入站流量”规则在网络安全组和 Azure 防火墙中的优先级最高。


## <a name="request-jit-access-via-asc"></a>通过 ASC 请求 JIT 访问

若要通过 ASC 请求访问 VM：

1. 在“实时 VM 访问”下，选择“已配置”选项卡。

2. 在“虚拟机”下，单击要请求访问的 VM。 这会勾选该 VM。


    - “连接详细信息”列中的图标指示是在 NSG 还是 FW 中启用了 JIT。 如果同时在 NSG 和 FW 中启用了 JIT，只会显示防火墙图标。

    - "**连接详细信息**" 列提供连接 VM 及其开放端口所需的信息。

      ![请求实时访问](./media/security-center-just-in-time/request-just-in-time-access.png)

3. 单击“请求访问”。 此时会打开“请求访问”窗口。

      ![JIT 详细信息](./media/security-center-just-in-time/just-in-time-details.png)

4. 在“请求访问”下，为每个 VM 配置要打开的端口、要为其打开该端口的源 IP 地址以及将打开该端口的时间范围。 仅可以请求对在实时策略中配置的端口的访问权限。 每个端口都有一个从实时策略派生的最大允许时间。

5. 单击“打开端口”。

> [!NOTE]
> 如果请求访问的用户使用代理，则“我的 IP”选项可能无法使用。 可能需要定义组织的完整 IP 地址范围。

## <a name="edit-a-jit-access-policy-via-asc"></a>通过 ASC 编辑 JIT 访问策略

可以对 VM 的现有实时策略进行以下更改：添加并配置要保护该 VM 的新端口，或更改与已保护的端口相关的任何其他设置。

编辑现有的 VM 实时策略：
1. 在“已配置”选项卡中，在“VM”下，通过单击 VM 对应的行内的三个点来选择要为其添加端口的 VM。 

1. 选择“编辑”。
1. 在“JIT VM 访问配置”下，可以编辑已保护的端口的现有设置，也可以添加新的自定义端口。 
  ![jit vm 访问](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>在 ASC 中审核 JIT 访问活动

可以使用日志搜索深入了解 VM 活动。 若要查看日志，请执行以下操作：

1. 在“实时 VM 访问”下，选择“已配置”选项卡。
2. 在“VM”下，通过单击 VM 对应的行内的三个点来选择要查看其信息的 VM，然后在菜单中选择“活动日志”。 此时会打开“活动日志”。

   ![选择“活动日志”](./media/security-center-just-in-time/select-activity-log.png)

   “活动日志”提供了该 VM 的以前操作的经筛选视图以及时间、日期和订阅。

可以通过选择“单击此处将所有项下载为 CSV”来下载日志信息。

修改筛选器并单击“应用”来创建搜索和日志。



## 在 Azure VM 边栏选项卡中配置 JIT 访问 <a name="jit-vm"></a>

为方便起见，可直接从 Azure 的 VM 边栏选项卡中使用 JIT 连接到 VM。

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>通过 Azure VM 边栏选项卡在 VM 上配置 JIT 访问

若要轻松地在 VM 间进行实时访问，可以将 VM 设置为仅允许从 VM 内直接进行实时访问。

1. 在 Azure 门户中，选择“虚拟机”。
2. 单击要实行实时访问限制的虚拟机。
3. 在菜单中，单击“配置”。
4. 在“实时访问”下，单击“启用实时策略”。 

此操作可为使用以下设置的 VM 启用实时访问：

- Windows 服务器：
    - RDP 端口 3389
    - 允许的最大访问次数为三个小时
    - 允许的源 IP 地址设置为“任何”
- Linux 服务器：
    - SSH 端口 22
    - 允许的最大访问次数为三个小时
    - 允许的源 IP 地址设置为“任何”
     
如果 VM 已启用实时访问，则在转到其配置页时，将看到实时访问已启用。可使用链接打开 Azure 安全中心中的策略，以便查看和更改设置。

![VM 中的 JIT 配置](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>通过 Azure VM 边栏选项卡请求对 VM 的 JIT 访问权限

在 Azure 门户中，尝试连接到 VM 时，Azure 会检查是否在该 VM 上配置实时访问策略。

- 如果在 VM 上配置了 JIT 策略，则可以单击“请求访问”，以便根据 VM 的 JIT 策略集进行访问。 

  >![jit 请求](./media/security-center-just-in-time/jit-request.png)

  使用以下默认参数请求访问权限：

  - **源 IP**：“任何”(*)（无法更改）
  - **时间范围**：三个小时（不能更改） <!--Isn't this set in the policy-->
  - **端口号**：在 Windows 中为 RDP 端口 3389/在 Linux 中为端口 22（可更改）

    > [!NOTE]
    > 批准对受 Azure 防火墙保护的 VM 的请求后，安全中心将为用户提供正确的连接详细信息（来自 DNAT 表的端口映射）用于连接 VM。

- 如果没有在 VM 上配置 JIT，将提示你配置 JIT 策略。

  ![jit 提示](./media/security-center-just-in-time/jit-prompt.png)

## 以编程方式在 VM 上配置 JIT 策略<a name="jit-program"></a>

可通过 REST API 和 PowerShell 设置和使用实时功能。

## <a name="jit-vm-access-via-rest-apis"></a>通过 REST API 对 VM 进行 JIT 访问

通过 Azure 安全中心 API 可使用实时 VM 访问功能。 可以通过此 API 获取有关配置 VM 的信息、添加新的 VM、请求访问 VM 等。 要详细了解实时 REST API，请参阅 [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)（JIT网络访问策略）。

## <a name="jit-vm-access-via-powershell"></a>通过 PowerShell 对 VM 进行 JIT 访问

要通过 PowerShell 使用实时 VM 访问解决方案，请使用正式的 Azure 安全中心 PowerShell cmdlet，具体为 `Set-AzJitNetworkAccessPolicy`。

下面的示例可对特定 VM 设置实时 VM 访问策略，并设置以下各项：

1.  关闭端口 22 和 3389。

2.  将每个的最大时间窗口设置为 3 小时，使它们能够按每个批准的请求打开。
3.  允许请求访问的用户控制源 IP 地址，允许用户对批准的实时访问请求建立成功会话。

在 PowerShell 中运行以下命令实现此目的：

1.  分配变量，保存 VM 的实时 VM 访问策略：

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  将 VM 实时 VM 访问策略插入数组：
    
        $JitPolicyArr=@($JitPolicy)

3.  对所选 VM 配置实时 VM 访问策略：
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>通过 PowerShell 请求访问 VM

在以下示例中，可以看到对特定 VM 的实时 VM 访问请求，其中请求端口 22 为特定 IP 地址打开，并持续特定时间：

在 PowerShell 中运行以下命令：
1.  配置 VM 请求访问属性

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  在数组中插入 VM 访问请求参数：

        $JitPolicyArr=@($JitPolicyVm1)
3.  发送请求访问权限（使用步骤 1 中获取的资源 ID）

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

有关详细信息，请参阅 PowerShell cmdlet 文档。

## <a name="next-steps"></a>后续步骤
在本文中，你已了解了安全中心中的实时 VM 访问如何帮助你控制对 Azure 虚拟机的访问。

若要了解有关安全中心的详细信息，请参阅以下文章：

- [设置安全策略](tutorial-security-policy.md) — 了解如何为 Azure 订阅和资源组配置安全策略。
- [管理安全建议](security-center-recommendations.md) — 了解建议如何帮助你保护 Azure 资源。
- [安全运行状况监视](security-center-monitoring.md) — 了解如何监视 Azure 资源的运行状况。
- [管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
- [监视合作伙伴解决方案](security-center-partner-solutions.md) — 了解如何监视合作伙伴解决方案的运行状态。
- [安全中心常见问题](security-center-faq.md) - 查找有关如何使用服务的常见问题。
- [Azure 安全性博客](https://blogs.msdn.microsoft.com/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。


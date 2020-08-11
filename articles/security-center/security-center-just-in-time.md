---
title: Azure 安全中心中的实时虚拟机访问 | Microsoft Docs
description: 本文档演示如何使用 Azure 安全中心中的实时 VM 访问 (JIT) 来控制对 Azure 虚拟机的访问。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 88f1924f69aed350b39f953cb7503a0dde9ca9ad
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056306"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>使用实时访问保护管理端口

使用 Azure 安全中心的实时 (JIT) 虚拟机 (VM) 访问功能锁定到你的 Azure 虚拟机的入站流量。 如果需要连接到 VM，这会减少攻击的暴露，同时提供轻松访问。

有关 JIT 如何工作以及底层逻辑的完整说明，请参阅[实时说明](just-in-time-explained.md)。

本页介绍如何在安全程序中包含 JIT。 将了解如何执行以下操作： 

- **在你的 vm 上启用 jit** -可以使用你自己的自定义选项为一个或多个使用安全中心、PowerShell 或 REST API 的 VM 启用 jit。 或者，可以从 Azure 虚拟机使用默认的硬编码参数启用 JIT。 启用后，JIT 会通过在网络安全组中创建规则来锁定到 Azure Vm 的入站流量。
- **请求访问已启用 JIT 的 VM** -JIT 的目标是确保即使你的入站流量被锁定，安全中心仍可在需要时轻松地连接到 vm。 你可以从安全中心、Azure 虚拟机、PowerShell 或 REST API 请求对启用了 JIT 的 VM 的访问权限。
- **审核活动**-若要确保 vm 得到适当保护，请在常规安全检查过程中查看对启用了 JIT 的 vm 的访问。   



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式版|
|计价|标准层|
|支持的 Vm：|![是 ](./media/icons/yes-icon.png) 通过 Azure 资源管理器部署的虚拟机。<br>![没有 ](./media/icons/no-icon.png) 用经典部署模型部署的 vm。 [了解有关这些部署模型的详细信息](../azure-resource-manager/management/deployment-models.md)。<br>![Azure ](./media/icons/no-icon.png) 防火墙不受 Azure[防火墙管理器](https://docs.microsoft.com/azure/firewall-manager/overview)保护的任何 vm|
|必需的角色和权限：|"**读取**者" 和 " **SecurityReader** " 角色可以查看 JIT 状态和参数。<br>若要创建可用于 JIT 的自定义角色，请参阅[配置和使用 jit 所需的权限](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)。<br>若要为需要请求对 VM 的 JIT 访问权限的用户创建最小特权角色，而不执行其他任何 JIT 操作，请使用安全中心 GitHub 社区页面中的[JitLeastPrivilegedRole 脚本](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||


## <a name="enable-jit-vm-access"></a>启用 JIT VM 访问<a name="jit-configure"></a>

你可以使用安全中心或以编程方式为一个或多个 Vm 启用使用你自己的自定义选项的 JIT VM 访问。 

或者，可以从 Azure 虚拟机使用默认的硬编码参数启用 JIT。

每个选项都在下面的单独选项卡中介绍。

### <a name="azure-security-center"></a>[**Azure 安全中心**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>在 Azure 安全中心的 Vm 上启用 JIT<a name="jit-asc"></a>

![在 Azure 安全中心配置 JIT VM 访问](./media/security-center-just-in-time/jit-config-security-center.gif)

在安全中心，你可以启用和配置 JIT VM 访问。

1. 从安全中心的菜单中，选择 "**实时 VM 访问**"。

    此时会打开 "**实时 VM 访问**" 页面，并将 vm 分组为以下选项卡：

    - 已**配置**-已配置为支持实时 VM 访问的虚拟机。 对于每个 VM，"已配置" 选项卡显示：
        - 过去七天内批准的 JIT 请求数
        - 上次访问日期和时间
        - 配置的连接详细信息
        - 最后一个用户
    - **未配置-未**启用 jit 但可以支持 jit 的 vm。 建议为这些 Vm 启用 JIT。
    - **不受支持**-未启用 JIT 且不支持该功能的 vm。 VM 可能在此选项卡中，原因如下：
      - 缺少网络安全组 (NSG) -JIT 要求配置 NSG
      - 经典 VM-JIT 支持通过 Azure 资源管理器部署的 Vm，而不是 "经典部署"。 [详细了解经典 Vs Azure 资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)。
      - 其他-如果在订阅或资源组的安全策略中禁用了 JIT 解决方案，则 VM 可能在此选项卡中。

1. 在 "**未配置**" 选项卡上，将 vm 标记为使用 JIT 保护，并选择 **"在 VM 上启用 JIT"**。 

    此时将打开 "JIT VM 访问" 页，其中列出了安全中心建议保护的端口：
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    若要接受默认设置，请选择 "**保存**"。

1. 自定义 JIT 选项：

    - 通过 "**添加**" 按钮添加自定义端口。 
    - 通过从列表中选择一个默认端口来修改其中一个。

    对于每个端口 (自定义和默认) "**添加端口配置**" 窗格提供以下选项：

    - **协议**-批准请求时此端口允许的协议
    - **允许的源 ip**-批准请求时此端口允许的 IP 范围
    - **最大请求时间**-可以打开特定端口的最大时间范围

     1. 根据需要设置端口安全性。

     1. 选择“确定”。

1. 选择“保存”。



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>使用安全中心编辑启用了 JIT 的 VM 上的 JIT 配置<a name="jit-modify"></a>

你可以通过添加并配置新的端口来保护 vm 的实时配置，以保护该 VM，或更改与已保护端口相关的任何其他设置。

编辑 VM 的现有 JIT 规则：

1. 从安全中心的菜单中，选择 "**实时 VM 访问**"。

1. 在 "**配置**" 选项卡上，右键单击要向其添加端口的 VM，然后选择 "编辑"。 

    ![在 Azure 安全中心编辑 JIT VM 访问配置](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. 在“JIT VM 访问配置”下，可以编辑已保护的端口的现有设置，也可以添加新的自定义端口。

1. 完成编辑端口后，请选择 "**保存**"。
 


### <a name="azure-virtual-machines"></a>[**Azure 虚拟机**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>在 Azure 虚拟机中启用 Vm 上的 JIT

可以从 Azure 门户的 "Azure 虚拟机" 页面启用 VM 上的 JIT。

![在 Azure 虚拟机中配置 JIT VM 访问](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> 如果 VM 已启用实时，则当你转到其配置页时，你将看到实时启用，你可以使用该链接在安全中心打开 "实时 VM 访问" 页，并查看和更改设置。

1. 在 [Azure 门户](https://ms.portal.azure.com)中，搜索并选择“虚拟机”。 

1. 选择要通过 JIT 保护的虚拟机。

1. 在菜单中选择“配置”。

1. 在“实时访问”下，选择“启用实时” 。 

    这将使用以下默认设置为 VM 启用实时访问：

    - Windows 计算机：
        - RDP 端口 3389
        - 允许的最长访问时间为三小时
        - 允许的源 IP 地址设置为“任何”
    - Linux 计算机：
        - SSH 端口 22
        - 允许的最长访问时间为三小时
        - 允许的源 IP 地址设置为“任何”

1. 若要编辑这些值或将更多端口添加到 JIT 配置，请使用 Azure 安全中心的实时页面：

    1. 从安全中心的菜单中，选择 "**实时 VM 访问**"。

    1. 在 "**配置**" 选项卡上，右键单击要向其添加端口的 VM，然后选择 "编辑"。 

        ![在 Azure 安全中心编辑 JIT VM 访问配置](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. 在“JIT VM 访问配置”下，可以编辑已保护的端口的现有设置，也可以添加新的自定义端口。

    1. 完成编辑端口后，请选择 "**保存**"。


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>使用 PowerShell 启用 Vm 上的 JIT

若要从 PowerShell 启用实时 VM 访问，请使用官方 Azure 安全中心 PowerShell cmdlet `Set-AzJitNetworkAccessPolicy` 。

**示例**-使用以下规则启用特定 VM 上的实时 VM 访问：

* 关闭端口22和3389
* 为每个时间窗口设置最大时间范围3小时，以便每个批准的请求都可以打开它们
* 允许正在请求访问权限的用户控制源 IP 地址
* 允许正在请求访问权限的用户根据已批准的实时访问请求建立成功会话

以下 PowerShell 命令创建此 JIT 配置：

1. 为 VM 分配保存实时 VM 访问规则的变量：

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. 将 VM 实时 VM 访问规则插入到数组中：
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 在所选 VM 上配置实时 VM 访问规则：
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    使用-Name 参数指定 VM。 例如，若要为两个不同的 Vm （VM1 和 VM2）建立 JIT 配置，请使用： ```Set-AzJitNetworkAccessPolicy -Name VM1``` 和 ```Set-AzJitNetworkAccessPolicy -Name VM2``` 。


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>使用 REST API 在你的 Vm 上启用 JIT

通过 Azure 安全中心 API 可使用实时 VM 访问功能。 使用此 API 可获取有关配置的 Vm、添加新 Vm、请求对 VM 的访问权限等信息。 

有关详细信息，请参阅[JIT 网络访问策略](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)。


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>请求访问启用 JIT 的 VM

可以) 或以编程方式从安全中心或 Azure 虚拟机中的 Azure 门户 (请求对启用了 JIT 的 VM 的访问权限。

每个选项都在下面的单独选项卡中介绍。

### <a name="azure-security-center"></a>[**Azure 安全中心**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>从 Azure 安全中心请求访问启用 JIT 的 VM 

如果 VM 启用了 JIT，则必须请求访问以连接到该 VM。 无论你如何启用 JIT，你都可以通过任何受支持的方式请求访问。

![从安全中心请求 JIT 访问](./media/security-center-just-in-time/jit-request-security-center.gif)

1. 从 "实时**VM 访问**" 页中，选择 "**已配置**" 选项卡。

1. 标记要访问的虚拟机。

    - "**连接详细信息**" 列中的图标表示是否在网络安全组或防火墙上启用 JIT。 如果同时启用这两个，则只会显示防火墙图标。

    - “连接详细信息”列提供连接 VM 所需的信息，及其打开的端口。

1. 选择“请求访问权限”。**** 此时会打开“请求访问”窗口。

1. 在“请求访问”下，为每个 VM 配置要打开的端口、要为其打开该端口的源 IP 地址以及将打开该端口的时间范围。 只能请求访问配置的端口。 每个端口都具有从已创建的 JIT 配置派生的最大允许时间。

1. 选择“打开端口”。****

> [!NOTE]
> 如果请求访问的用户使用代理，则“我的 IP”选项可能无法使用。 可能需要定义组织的完整 IP 地址范围。



### <a name="azure-virtual-machines"></a>[**Azure 虚拟机**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>从 Azure 虚拟机的 "连接" 页请求访问启用了 JIT 的 VM

如果 VM 启用了 JIT，则必须请求访问以连接到该 VM。 无论你如何启用 JIT，你都可以通过任何受支持的方式请求访问。

  >![jit 实时请求](./media/security-center-just-in-time/jit-request-vm.png)


请求从 Azure 虚拟机访问：

1. 在 Azure 门户中，打开 "虚拟机" 页。

1. 选择要连接到的 VM，并打开 "**连接**" 页。

    Azure 会检查是否已在该 VM 上启用了 JIT。

    - 如果没有为 VM 启用 JIT，系统将提示你启用该功能。

    - 如果启用了 JIT，则选择 "**请求访问权限**" 可通过为该 VM 配置的请求 IP、时间范围和端口传递访问请求。

> [!NOTE]
> 批准对受 Azure 防火墙保护的 VM 的请求后，安全中心将为用户提供正确的连接详细信息（来自 DNAT 表的端口映射）用于连接 VM。



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>使用 PowerShell 请求访问启用了 JIT 的 VM

在以下示例中，可以看到对特定 VM 的实时 VM 访问请求，其中请求端口 22 为特定 IP 地址打开，并持续特定时间：

在 PowerShell 中运行以下命令：

1. 配置 VM 请求访问属性：

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. 在数组中插入 VM 访问请求参数：

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. 使用步骤1中的资源 ID 发送请求访问 () 

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

有关详细信息，请[参阅 PowerShell cmdlet 文档](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)。



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>使用 REST API 请求访问启用 JIT 的 Vm

通过 Azure 安全中心 API 可使用实时 VM 访问功能。 使用此 API 可获取有关配置的 Vm、添加新 Vm、请求对 VM 的访问权限等信息。 

有关详细信息，请参阅[JIT 网络访问策略](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)。

---








## <a name="audit-jit-access-activity-in-security-center"></a>审核安全中心的 JIT 访问活动

可以使用日志搜索深入了解 VM 活动。 若要查看日志，请执行以下操作：

1. 从 "实时**VM 访问**" 中，选择 "**已配置**" 选项卡。

1. 对于想要审核的 VM，请打开行末尾的省略号菜单。
 
1. 从菜单中选择 "**活动日志**"。

   ![选择实时 JIT 活动日志](./media/security-center-just-in-time/jit-select-activity-log.png)

   活动日志为该 VM 以及时间、日期和订阅提供了之前操作的筛选视图。

1. 若要下载日志信息，请选择 "以**CSV 格式下载**"。








## <a name="next-steps"></a>后续步骤

本文介绍了如何设置和使用实时 VM 访问。 若要了解为什么应使用 JIT，请阅读介绍它所防御的威胁的概念文章：

> [!div class="nextstepaction"]
> [JIT 说明](just-in-time-explained.md)
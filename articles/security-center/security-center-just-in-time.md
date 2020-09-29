---
title: Azure 安全中心中的实时虚拟机访问 | Microsoft Docs
description: 本文档说明 Azure 安全中心中的实时 VM 访问 (JIT) 如何帮助你控制对 Azure 虚拟机的访问。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 4a709527c0de2e092bcca2bbd9bc596aa0eb4cc0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440723"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>使用实时访问保护管理端口

使用 Azure 安全中心的实时 (JIT) 虚拟机 (VM) 访问功能来锁定发往 Azure 虚拟机的入站流量。 这可以降低遭受攻击的可能性，同时在你需要连接到 VM 时让你能够轻松进行访问。

有关 JIT 工作原理和底层逻辑的完整说明，请参阅[有关实时的说明](just-in-time-explained.md)。

本页介绍如何在安全程序中包括 JIT。 将了解如何执行以下操作： 

- 在 VM 上启用 JIT - 可以使用安全中心、PowerShell 或 REST API，为一个或多个 VM 启用采用你自己的自定义选项的 JIT。 也可以从 Azure 虚拟机使用默认的硬编码参数启用 JIT。 启用后，JIT 会通过在网络安全组中创建规则来锁定发往 Azure VM 的入站流量。
- 请求访问已启用 JIT 的 VM - JIT 的目标是确保即使在入站流量被锁定的情况下，你也能在需要时通过安全中心轻松进行访问，以便连接到 VM。 你可以通过安全中心、Azure 虚拟机、PowerShell 或 REST API 请求对启用了 JIT 的 VM 进行访问。
- 审核活动 - 若要确保 VM 得到适当保护，请在常规安全检查过程中评审对启用了 JIT 的 VM 的访问。   



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|需要 [用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|支持的 VM：|![是](./media/icons/yes-icon.png) 通过 Azure 资源管理器部署的 VM。<br>![否](./media/icons/no-icon.png) 通过经典部署模型部署的 VM。 [了解有关这些部署模型的详细信息](../azure-resource-manager/management/deployment-models.md)。<br>![否](./media/icons/no-icon.png) 由 [Azure 防火墙管理器](https://docs.microsoft.com/azure/firewall-manager/overview)控制的 Azure 防火墙保护的 VM|
|所需角色和权限：|“读取者”角色和“安全读取者”角色都可以查看 JIT 状态和参数。<br>若要创建可与 JIT 配合使用的自定义角色，请参阅[配置和使用 JIT 时需要哪些权限？](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)。<br>若要为那些需要请求对 VM 进行 JIT 访问而不执行其他 JIT 操作的用户创建最小特权角色，请使用安全中心 GitHub 社区页面中的 [Set-JitLeastPrivilegedRole 脚本](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||


## <a name="enable-jit-vm-access"></a>启用 JIT VM 访问 <a name="jit-configure"></a>

可以使用安全中心或通过编程方式，为一个或多个 VM 启用采用你自己的自定义选项的 JIT VM 访问。 

或者，可以从 Azure 虚拟机使用默认的硬编码参数启用 JIT。

这些选项中的每一个都在下面的单独选项卡中进行了介绍。

### <a name="azure-security-center"></a>[**Azure 安全中心**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>从 Azure 安全中心对 VM 启用 JIT <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="在 Azure 安全中心配置 JIT VM 访问&quot;:::

可以从安全中心启用和配置 JIT VM 访问。

1. 打开 Azure Defender 仪表板，并从 &quot;高级保护&quot; 区域中选择 " **实时 VM 访问**"。

    此时会打开“实时 VM 访问”页，你的 VM 会分组到以下多个选项卡中：

    - 已配置 - 已配置为支持实时 VM 访问的 VM。 对于每个 VM，“已配置”选项卡会显示：
        - 过去七天批准的 JIT 请求数
        - 上次访问日期和时间
        - 已配置的连接详细信息
        - 上一个用户
    - 未配置 - 未启用 JIT 但可以支持 JIT 的 VM。 建议为这些 VM 启用 JIT。
    - 不支持 - 未启用 JIT 且不支持该功能的 VM。 你的 VM 出现在此选项卡中可能是因为以下原因：
      - 缺少网络安全组 (NSG) - JIT 要求配置 NSG
      - 经典 VM - JIT 支持通过 Azure 资源管理器而非“经典部署”部署的 VM。 [详细了解经典部署模型与 Azure 资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)。
      - 其他 - 如果在订阅或资源组的安全策略中禁用了 JIT 解决方案，则你的 VM 可能在此选项卡中。

1. 从“未配置”选项卡上，将 VM 标记为使用 JIT 进行保护，然后选择“在 VM 上启用 JIT”。  

    此时会打开“JIT VM 访问”页，其中列出了安全中心建议保护的端口：
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    若要接受默认设置，请选择“保存”。

1. 若要自定义 JIT 选项，请执行以下操作：

    - 使用“添加”按钮添加自定义端口。 
    - 从列表中选择默认端口之一，对其进行修改。

    “添加端口配置”窗格为每个端口（自定义端口和默认端口）提供以下选项：

    - 协议 - 批准某个请求时此端口允许的协议
    - 允许的源 IP - 批准某个请求时此端口允许的 IP 范围
    - 最大请求时间 - 可以打开特定端口的最大时间范围

     1. 根据需要设置端口安全性。

     1. 选择“确定”  。

1. 选择“保存”。



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>使用安全中心编辑启用了 JIT 的 VM 上的 JIT 配置 <a name="jit-modify"></a>

可以对 VM 的实时配置进行以下修改：添加并配置要针对该 VM 进行保护的新端口，或更改与已保护的端口相关的任何其他设置。

若要编辑 VM 的现有 JIT 规则，请执行以下操作：

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 在“已配置”选项卡上，右键单击要向其添加端口的 VM，然后选择“编辑”。 

    ![在 Azure 安全中心编辑 JIT VM 访问配置](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. 在“JIT VM 访问配置”下，可以编辑已保护的端口的现有设置，也可以添加新的自定义端口。

1. 编辑完端口后，选择“保存”。
 


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

    1. 从安全中心的菜单中，选择“实时 VM 访问”。

    1. 在“已配置”选项卡上，右键单击要向其添加端口的 VM，然后选择“编辑”。 

        ![在 Azure 安全中心编辑 JIT VM 访问配置](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. 在“JIT VM 访问配置”下，可以编辑已保护的端口的现有设置，也可以添加新的自定义端口。

    1. 编辑完端口后，选择“保存”。


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>使用 PowerShell 对 VM 启用 JIT

若要通过 PowerShell 启用实时 VM 访问，请使用正式的 Azure 安全中心 PowerShell cmdlet `Set-AzJitNetworkAccessPolicy`。

示例 - 按照以下规则对特定 VM 启用实时 VM 访问：

* 关闭端口 22 和 3389
* 将每个端口的最大时间窗口设置为 3 小时，使它们能够按已批准的请求打开
* 允许正在请求访问权限的用户控制源 IP 地址
* 允许正在请求访问权限的用户在实时访问请求获得批准后建立一个成功的会话

以下 PowerShell 命令创建此 JIT 配置：

1. 分配变量，以便保存 VM 的实时 VM 访问规则：

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

1. 将 VM 的实时 VM 访问规则插入数组：
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 对所选 VM 配置实时 VM 访问规则：
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    使用 -Name 参数指定 VM。 例如，若要为两个不同的 VM（VM1 和 VM2）建立 JIT 配置，请使用 ```Set-AzJitNetworkAccessPolicy -Name VM1``` 和 ```Set-AzJitNetworkAccessPolicy -Name VM2```。


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>使用 REST API 对 VM 启用 JIT

通过 Azure 安全中心 API 可使用实时 VM 访问功能。 使用此 API 获取有关已配置 VM 的信息、添加新的 VM、请求访问 VM，等等。 

有关详细信息，请参阅 [JIT 网络访问策略](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)。


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>请求访问启用了 JIT 的 VM

可以通过 Azure 门户（在安全中心或 Azure 虚拟机中）或编程方式请求访问启用了 JIT 的 VM。

这些选项中的每一个都在下面的单独选项卡中进行了介绍。

### <a name="azure-security-center"></a>[**Azure 安全中心**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>从 Azure 安全中心请求访问启用了 JIT 的 VM 

如果 VM 启用了 JIT，则必须请求连接到它所需的访问权限。 不管你启用 JIT 的方式如何，你都可以通过任何受支持的方式请求访问权限。

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="在 Azure 安全中心配置 JIT VM 访问&quot;:::

可以从安全中心启用和配置 JIT VM 访问。

1. 打开 Azure Defender 仪表板，并从 &quot;高级保护&quot; 区域中选择 ":::

1. 从“实时 VM 访问”页选择“已配置”选项卡。 

1. 标记要访问的 VM。

    - “连接详细信息”列中的图标指示是对网络安全组还是对防火墙启用了 JIT。 如果对二者均启用了 JIT，则只会显示防火墙图标。

    - “连接详细信息”列提供连接 VM 所需的信息，及其打开的端口。

1. 选择“请求访问权限”。**** 此时会打开“请求访问”窗口。

1. 在“请求访问”下，为每个 VM 配置要打开的端口、要为其打开该端口的源 IP 地址以及将打开该端口的时间范围。 只能请求访问已配置的端口。 每个端口都有一个从已创建的 JIT 配置派生的最大允许时间。

1. 选择“打开端口”。****

> [!NOTE]
> 如果请求访问的用户使用代理，则“我的 IP”选项可能无法使用。 可能需要定义组织的完整 IP 地址范围。



### <a name="azure-virtual-machines"></a>[**Azure 虚拟机**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>从 Azure 虚拟机的连接页请求访问启用了 JIT 的 VM

如果 VM 启用了 JIT，则必须请求连接到它所需的访问权限。 不管你启用 JIT 的方式如何，你都可以通过任何受支持的方式请求访问权限。

  >![jit（实时）请求](./media/security-center-just-in-time/jit-request-vm.png)


若要请求从 Azure 虚拟机进行访问，请执行以下操作：

1. 在 Azure 门户中，打开虚拟机页面。

1. 选择要连接到的 VM，然后打开“连接”页。

    Azure 会查看是否已在该 VM 上启用了 JIT。

    - 如果没有为该 VM 启用 JIT，系统会提示你启用它。

    - 如果启用了 JIT，则选择“请求访问”，以便传递访问请求，其中包含已为该 VM 配置的请求 IP、时间范围和端口。

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
        
1. 发送访问请求（使用步骤 1 中的资源 ID）

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

有关详细信息，请参阅 [PowerShell cmdlet 文档](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)。



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>使用 REST API 请求访问启用了 JIT 的 VM

通过 Azure 安全中心 API 可使用实时 VM 访问功能。 使用此 API 获取有关已配置 VM 的信息、添加新的 VM、请求访问 VM，等等。 

有关详细信息，请参阅 [JIT 网络访问策略](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)。

---








## <a name="audit-jit-access-activity-in-security-center"></a>审核安全中心的 JIT 访问活动

可以使用日志搜索深入了解 VM 活动。 若要查看日志，请执行以下操作：

1. 从“实时 VM 访问”选择“已配置”选项卡 。

1. 对于要审核的 VM，请打开行末尾的省略号菜单。
 
1. 从菜单中选择“活动日志”。

   ![选择实时 (JIT) 活动日志](./media/security-center-just-in-time/jit-select-activity-log.png)

   活动日志提供了一个经筛选的视图，其中包含以前针对该 VM 进行的操作以及时间、日期和订阅。

1. 若要下载日志信息，请选择“以 CSV 格式下载”。








## <a name="next-steps"></a>后续步骤

本文介绍了如何设置和使用实时 VM 访问权限。 若要了解为什么应使用 JIT，请阅读以下概念文章，其中介绍了 JIT 抵御的威胁：

> [!div class="nextstepaction"]
> [有关 JIT 的说明](just-in-time-explained.md)
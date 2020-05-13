---
title: Azure 安全中心中的数据收集 | Microsoft Docs
description: 本文介绍如何安装 Log Analytics 代理，以及如何设置 Log Analytics 工作区来存储所收集的数据。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: memildin
ms.openlocfilehash: b1edb0791e80a8503e5ecba3154d7b421206272c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198620"
---
# <a name="data-collection-in-azure-security-center"></a>Azure 安全中心中的数据收集
安全中心从 Azure 虚拟机（Vm）、虚拟机规模集、IaaS 容器和非 Azure （包括本地）计算机收集数据以监视安全漏洞和威胁。 数据是使用 Log Analytics 代理收集的，该代理从计算机中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和已登录的用户。 Log Analytics 代理还将故障转储文件复制到工作区。

需要收集数据才能查看缺少的更新、配置错误的 OS 安全设置、endpoint protection 状态以及健康和威胁防护。 

本文介绍如何安装 Log Analytics 代理，以及如何设置 Log Analytics 工作区来存储所收集的数据。 这两项操作都需要启用数据收集。 

> [!NOTE]
> - 只需要对计算资源（Vm、虚拟机规模集、IaaS 容器和非 Azure 计算机）进行数据收集。 即使未预配代理，也能从 Azure 安全中心受益；但是，安全性会受到限制，并且上面列出的功能不受支持。  
> - 有关支持的平台列表，请参阅 [Azure 安全中心支持的平台](security-center-os-coverage.md)。
> - 将数据存储在 Log Analytics 中，无论是使用新工作区还是现有工作区，都可能会产生额外的数据存储费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>启用 Log Analytics 代理的自动预配<a name="auto-provision-mma"></a>

若要从计算机中收集数据，应安装 Log Analytics 代理。 可以自动完成代理的安装（推荐），也可以手动安装该代理。 默认情况下，自动设置处于关闭状态。

当启用自动预配时，安全中心会在所有支持的 Azure Vm 和创建的任何新虚拟机上部署 Log Analytics 代理。 建议使用自动设置，但如果需要，可以手动安装代理（请参阅[手动安装 Log Analytics 代理](#manual-agent)）。


若要启用对 Log Analytics 代理的自动预配，请执行以下操作：
1. 在门户的 "安全中心" 菜单中，选择 "**定价 & 设置**"。
2. 选择相关订阅。

   ![选择订阅][7]

3. 选择 "**数据收集**"。
4. 在“自动设置”下，选择“打开”以启用自动设置********。
5. 选择“保存”  。 代理会在15分钟内部署到所有 Vm。 

>[!TIP]
> 如果需要设置工作区，则代理安装可能需要长达25分钟的时间。

   ![启用自动设置][1]

>[!NOTE]
> - 有关如何预配现有安装的说明，请参阅[对现有的代理安装进行自动预配](#preexisting)。
> - 有关手动预配的说明，请参阅[手动安装 Log Analytics 代理扩展](#manual-agent)。
> - 有关关闭自动预配的说明，请参阅[关闭自动预配](#offprovisioning)。
> - 有关如何使用 PowerShell 加入安全中心的说明，请参阅[使用PowerShell 自动加入 Azure 安全中心](security-center-powershell-onboarding.md)。
>

## <a name="workspace-configuration"></a>工作区配置
安全中心收集的数据存储在 Log Analytics 工作区中。 可以从安全中心创建的工作区中存储的 Azure Vm 收集数据，也可以从已创建的现有工作区中收集数据。 

工作区配置是按订阅设置的，多个订阅可以使用同一个工作区。

### <a name="using-a-workspace-created-by-security-center"></a>使用安全中心创建的工作区

安全中心可以自动创建用于存储数据的默认工作区。 

选择安全中心创建的工作区：

1. 在“默认工作区配置”下，选择“使用安全中心创建的工作区”。****
   ![选择定价层][10] 

1. 单击“保存”  。<br>
    安全中心会在该地理位置创建新的资源组和默认工作区，并将代理连接到该工作区。 工作区和资源组的命名约定是：<br>
   **工作区：DefaultWorkspace-[subscription-ID]-[geo]<br> 资源组：DefaultResourceGroup-[geo]**

   如果订阅包含多个地理位置中的 VM，则安全中心会创建多个工作区。 创建多个工作区的目的是维护数据隐私规则。
1. 安全中心将会根据针对订阅设置的定价层，在工作区中自动启用安全中心解决方案。 

> [!NOTE]
> 安全中心创建的工作区的 Log Analytics 定价层不会影响安全中心计费。 安全中心的计费始终依据工作区上安装的以下安全中心安全策略和解决方案。 对于“免费层”，安全中心将在默认工作区中启用 *SecurityCenterFree* 解决方案。 对于“标准层”，安全中心将在默认工作区中启用 *Security* 解决方案。
> 将数据存储在 Log Analytics 可能会产生额外的数据存储费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

有关现有 log analytics 帐户的详细信息，请参阅[现有 log analytics 客户](./faq-azure-monitor-logs.md)。

### <a name="using-an-existing-workspace"></a>使用现有工作区

如果已有现有 Log Analytics 工作区，可能需要使用同一工作区。

若要使用现有 Log Analytics 工作区，必须对该工作区拥有读取和写入权限。

> [!NOTE]
> 在现有工作区中启用的解决方案将应用到与该工作区相连接的 Azure VM。 对于付费的解决方案，这可能会产生额外的费用。 出于数据隐私的考虑，请确保所选工作区位于适当的地理区域。
> 在 log analytics 中存储数据可能会产生额外的数据存储费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

选择现有 Log Analytics 工作区的具体步骤：

1. 在“默认工作区配置”下，选择“使用其他工作区”********。

   ![选择现有工作区][2]

2. 从下拉菜单中，选择一个工作区，用于存储所收集的数据。

   > [!NOTE]
   > 下拉菜单提供跨所有订阅的所有工作区。 请参阅[跨订阅工作区选择](security-center-enable-data-collection.md#cross-subscription-workspace-selection)以获取详细信息。 必须有权访问该工作区。
   >
   >

3. 选择“保存”  。
4. 选择“保存”后，系统会询问是否要重新配置以前已连接到默认工作区的受监视 VM。****

   - 如果希望仅在新 Vm 上应用新的工作区设置，请选择 "**否**"。 新的工作区设置仅适用于新的代理安装;未安装 Log Analytics 代理的新发现的虚拟机。
   - 如果希望在所有 VM 上应用新的工作区设置，请选择“是”****。 此外，所有连接到安全中心创建的工作区的 VM 也都会重新连接到新的目标工作区。

   > [!NOTE]
   > 如果选择“是”，不得删除安全中心创建的工作区，除非所有 VM 已重新连接到新的目标工作区。 如果过早删除工作区，此操作将会失败。
   >
   >

   - 选择“取消”****，以取消该操作。

     ![选择现有工作区][3]

5. 为要设置 Log Analytics 代理的所需工作区选择定价层。 <br>若要使用现有工作区，请设置该工作区的定价层。 这会在该工作区中安装一个安全中心解决方案（如果尚不存在）。

    a.  在安全中心主菜单中，选择 "**定价 & 设置**"。
     
    b.  选择要在其中连接代理的所需工作区。
        ![选择工作区][7] c. 设置定价层。
        ![选择定价层][9]
   
   >[!NOTE]
   >如果工作区中已启用 **Security** 或 **SecurityCenterFree** 解决方案，则会自动设置定价层。 

## <a name="cross-subscription-workspace-selection"></a>跨订阅工作区选择
选择用于存储数据的工作区时，跨所有订阅的所有工作区可用。 通过跨订阅工作区选择，可以从不同订阅中运行的虚拟机收集数据并将其存储在所选的工作区中。 如果在组织中使用集中式工作区，并想要使用该工作区来收集安全数据，则这种选择非常有用。 有关如何管理工作区的详细信息，请参阅[管理工作区访问权限](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)。


## <a name="data-collection-tier"></a>数据收集层
在 Azure 安全中心选择数据收集层只会影响 Log Analytics 工作区中安全事件的存储。 Log Analytics 代理仍将收集和分析 Azure 安全中心的威胁防护所需的安全事件，而不考虑你选择在 Log Analytics 工作区中存储哪一层安全事件（如果有）。 选择在工作区中存储安全事件将允许在工作区中调查、搜索和审核这些事件。 
> [!NOTE]
> 在 log analytics 中存储数据可能会产生额外的数据存储费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。
> 
> 可以根据要在工作区中存储的四组事件为订阅和工作区选择正确的筛选策略： 

- **无** - 禁用安全事件存储。 这是默认设置。
- **** 最小 – 一个较小事件集，适合希望最大程度地减小事件量的客户。
- **** 通用 – 这是一个事件集，可满足大多数客户的需求，使他们可以进行完整的审核跟踪。
- **** 所有事件 - 适用于想要确保存储所有事件的客户。


> [!NOTE]
> 这些安全事件集仅在安全中心的标准层上可用。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
这些集合专门用于典型应用场景。 请务必先评估哪个事件集适合你的需求，再进行实现。
>
>

为了确定属于通用**** 和最小**** 事件集的事件，我们与客户进行协作，参照行业标准，了解了每个事件及其使用情况的未筛选频率。 我们在此过程中使用了以下准则：

- **** 最小 - 确保此集只涵盖可能指示成功违反的事件以及数量很少的重要事件。 例如，此集包含用户成功和失败的登录（事件 Id 4624、4625），但它不包含用于审核的重要信息，但对于检测并无意义，它的数量相对较高。 此集的大多数数据量是登录事件和进程创建事件（事件 ID 4688）。
- **** 通用 - 提供此集中的完整用户审核跟踪。 例如，此集包含用户登录名和用户注销（事件 ID 4634）。 我们加入审核操作，如安全组更改、关键域控制器 Kerberos 操作以及行业组织建议的其他事件。

数量非常少的事件包含在通用集中，因为在所有事件中选择该集的主要动机是为了减少数量，而不是筛选出特定事件。

下面是对每个集的安全和 App Locker 事件 ID 的完整分类：

| 数据层 | 收集的事件指示器 |
| --- | --- |
| 最小 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 通用 | 1、299、300、324、340、403、404、410、411、412、413、431、500、501、1100、1102、1107、1108、4608、4610、4611、4614、4622、 |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - 如果使用组策略对象 (GPO)，建议启用审核策略过程创建事件 4688 以及事件 4688 内的 CommandLine 字段。** 有关过程创建事件 4688 的详细信息，请参阅安全中心的[常见问题解答](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)。 有关这些审核策略的详细信息，请参阅[审核策略建议](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)。
> -  若要为[自适应应用程序控件](security-center-adaptive-application.md)启用数据收集，安全中心会在审核模式下配置本地 AppLocker 策略以允许所有应用程序。 这将导致 AppLocker 生成事件，然后由安全中心收集和利用这些事件。 请务必注意，不会在已配置 AppLocker 策略的任何计算机上配置此策略。 
> - 若要收集 Windows 筛选平台[事件 ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)，需要启用[审核筛选平台连接](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

选择筛选策略的具体步骤：
1. 在 "**数据收集**" 页上，选择 "**安全事件**" 下的筛选策略。
2. 选择“保存”  。

   ![选择筛选策略][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>预先存在的代理安装的情况下的自动预配<a name="preexisting"></a> 

以下用例指定在已安装代理或扩展的情况下如何进行自动预配。 

- Log Analytics 代理安装在计算机上，而不是作为扩展（直接代理）<br>
如果 Log Analytics 代理直接安装在 VM 上（而不是 Azure 扩展），则安全中心将安装 Log Analytics 代理扩展，并可能将 Log Analytics 代理升级到最新版本。
安装的代理将继续向其已配置的工作区报告，此外还会向安全中心（在 Windows 计算机上支持多宿主）中配置的工作区进行报告。
如果配置的工作区是一个用户工作区（而不是安全中心的默认工作区），则需要在该工作区上安装 "security/" securityFree "解决方案，以便安全中心开始处理向该工作区报告的 Vm 和计算机的事件。<br>
<br>
对于 Linux 计算机，尚不支持代理多宿主，因此，如果检测到现有的代理安装，则不会发生自动预配，并且不会更改计算机的配置。
<br>
对于订阅上的现有计算机载入到2019-03-17 之前的安全中心，检测到现有代理时，将不会安装 Log Analytics 代理扩展，并且计算机将不会受到影响。 对于这些计算机，请参阅 "解决计算机上的监视代理运行状况问题" 建议，以解决这些计算机上的代理安装问题。

  
- 计算机上已安装 System Center Operations Manager 代理<br>
安全中心会将 Log Analytics 代理扩展并排安装到现有 Operations Manager。 现有的 Operations Manager 代理将继续正常向 Operations Manager 服务器报告。 Operations Manager 代理和 Log Analytics 代理共享公共运行库，在此过程中将更新为最新版本。 如果安装了 Operations Manager 代理版本2012，则**不**启用自动预配。<br>

- 存在现有的 VM 扩展<br>
    - 将 Monitoring Agent 作为扩展安装时，扩展配置仅允许向单个工作区报告。 安全中心不会覆盖用户工作区的现有连接。 安全中心会在已连接的工作区中存储来自 VM 的安全数据，前提是已在其上安装了 "安全" 或 "securityFree" 解决方案。 在此过程中，安全中心可将扩展版本升级到最新版本。  
    - 若要查看现有扩展将数据发送到哪个工作区，请运行测试来[验证与 Azure 安全中心的连接](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/)。 或者，你可以打开 Log Analytics 工作区，选择一个工作区，选择 VM，然后查看 Log Analytics 代理连接。 
    - 如果你的环境中的 Log Analytics 代理安装在客户端工作站上并向现有 Log Analytics 工作区报告，请查看[Azure 安全中心支持的操作系统](security-center-os-coverage.md)列表，以确保你的操作系统受支持。 有关详细信息，请参阅[现有 log analytics 客户](./faq-azure-monitor-logs.md)。
 
### <a name="turn-off-automatic-provisioning"></a>关闭自动预配 <a name="offprovisioning"></a>
随时可以关闭资源的自动预配，在安全策略中关闭此设置即可。 


1. 返回到“安全中心”主菜单，选择“安全策略”。
2. 在要禁用自动设置的订阅的行中，单击 "**编辑设置**"。
3. 在 "**安全策略-数据收集**" 页的 "**自动设置**" 下，选择 "**关闭**"。
4. 选择“保存”  。

   ![禁用自动预配][6]

自动预配处于禁用状态（关闭）时，不会显示默认的工作区配置部分。

如果以前在代理上之前关闭自动预配，则不会在新的 Vm 上进行设置。

 
> [!NOTE]
>  禁用自动预配不会从预配代理的 Azure Vm 删除 Log Analytics 代理。 有关删除 OMS 扩展的信息，请参阅[如何删除安全中心安装的 OMS 扩展](faq-data-collection-agents.md#remove-oms)。
>
    
## <a name="manual-agent-provisioning"></a>手动代理预配 <a name="manual-agent"></a>
 
可以通过多种方式手动安装 Log Analytics 代理。 手动安装时，请务必禁用自动预配。

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite VM 扩展部署 

你可以手动安装 Log Analytics 代理，因此安全中心可以从 Vm 收集安全数据，并提供建议和警报。
1. 选择自动预配 - 关闭。
2. 创建工作区，并为要设置 Log Analytics 代理的工作区设置定价层：

   a.  在安全中心主菜单中，选择“安全策略”****。
     
   b.  选择要在其中连接代理的工作区。 确保该工作区位于安全中心内所用的同一个订阅中，并且你对该工作区拥有读/写权限。
       ![选择工作区][8]
3. 设置定价层。
   ![选择定价层][9] 
   >[!NOTE]
   >如果工作区中已启用 **Security** 或 **SecurityCenterFree** 解决方案，则会自动设置定价层。 
   > 

4. 若要使用资源管理器模板在新 VM 上部署代理，请安装 OMS 虚拟机扩展：

   a.  [安装适用于 Windows 的 OMS 虚拟机扩展](../virtual-machines/extensions/oms-windows.md)
    
   b.  [安装适用于 Linux 的 OMS 虚拟机扩展](../virtual-machines/extensions/oms-linux.md)
5. 若要在现有 VM 上部署扩展，请遵照[收集有关 Azure 虚拟机的数据](../azure-monitor/learn/quick-collect-azurevm.md)中的说明。

   > [!NOTE]
   > “收集事件和性能数据”部分是可选的。****
   >
6. 若要使用 PowerShell 部署扩展，请使用以下 PowerShell 示例：
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. 转到“Log Analytics”并单击“高级设置”。********
    
      ![设置 Log Analytics][11]

   2. 复制“工作区 ID”和“主密钥”的值。********
  
      ![复制值][12]

   3. 在公共配置和专用配置中填充以下值：
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - 在 Windows VM 上安装时：
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - 在 Linux VM 上安装时：
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> 有关如何使用 PowerShell 加入安全中心的说明，请参阅[使用PowerShell 自动加入 Azure 安全中心](security-center-powershell-onboarding.md)。

## <a name="troubleshooting"></a>疑难解答

-   若要识别自动预配安装问题，请参阅[监视代理运行状况问题](security-center-troubleshooting-guide.md#mon-agent)。

-  若要确定监视代理网络要求，请参阅[监视代理网络要求故障排除](security-center-troubleshooting-guide.md#mon-network-req)。
-   若要确定手动载入问题，请参阅[如何排查 Operations Management Suite 载入问题](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)。

- 确定不受监视的 Vm 和计算机问题：

    如果计算机未运行 Log Analytics 代理扩展，则安全中心不会监视 VM 或计算机。 计算机上可能已安装了本地代理，例如 OMS 直接代理或 System Center Operations Manager 代理。 装有这些代理的计算机被标识为未受监视，因为安全中心不完全支持这些代理。 若要充分利用安全中心的所有功能，需要 Log Analytics 代理扩展。

    有关安全中心无法成功监视为自动预配初始化的 Vm 和计算机的原因的详细信息，请参阅[监视代理运行状况问题](security-center-troubleshooting-guide.md#mon-agent)。


## <a name="next-steps"></a>后续步骤
本文介绍了数据收集和自动设置在安全中心中的工作方式。 若要了解有关安全中心的详细信息，请参阅以下页面：

* [Azure 安全中心常见问题解答](faq-general.md)-- 查找有关使用服务的常见问题。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png

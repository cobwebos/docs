---
title: Azure Stack 1901 更新 |Microsoft Docs
description: 了解有关 Azure Stack 集成系统，包括最新内容、 1901年更新的已知的问题，以及下载更新的位置。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 94c7b5c81785c3d6fba31396f5812a1308817e33
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301105"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 更新

*适用于：Azure Stack 集成系统*

本文介绍 1901年更新包的内容。 更新包括改进、 修复程序和 Azure Stack 的此版本的新功能。 本文还介绍了此版本中的已知的问题，并包括用于下载更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1901 更新内部版本号是**1.1901.0.95**。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 请务必安装[最新的 Azure Stack 修补程序](#azure-stack-hotfixes)为 1811 到 1901年更新 Azure Stack 之前。

Azure Stack 修补程序都只适用于 Azure Stack 集成系统;不要尝试在 ASDK 上安装修补程序。

> [!TIP]  
> 订阅以下*RSS*或*Atom*源，以保持与 Azure Stack 修补程序：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1809**：[KB 4481548 – Azure Stack 修补程序 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**：当前没有修补程序可用。
- **1901**:当前没有修补程序可用。

## <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
- 安装[最新的 Azure Stack 修补程序](#azure-stack-hotfixes)为之前更新到 1901年 1811 （如果有）。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 此外查看活动警报，并解决任何需要执行操作：

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

## <a name="new-features"></a>新增功能

此更新包含以下适用于 Azure Stack 的新功能和改进：

- 在 Azure Stack 启用的托管的映像你只能创建托管的通用 VM （非托管和托管） 上创建托管的映像对象的磁盘今后的 Vm。 有关详细信息，请参阅[Azure Stack 托管磁盘](user/azure-stack-managed-disk-considerations.md#managed-images)。

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Bug 修复-`Import-AzureRmContext`以正确反序列化已保存的令牌。  
   * **AzureRm.Resources**  
         Bug 修复-`Get-AzureRmResource`为查询大小写，不区分大小按资源类型。  
   * **Azure.Storage**  
         AzureRm 汇总模块现在包括已发布的版本 4.5.0 支持**api 版本 2017年-07-29**。  
   * **AzureRm.Storage**  
         AzureRm 汇总模块现在包括 5.0.4 已发布的版支持**api 版本 2017年-10-01**。  
   * **AzureRm.Compute**  
         添加了简单的参数设置`New-AzureRMVM`并`NewAzureRMVMSS`，`-ImageName`参数支持指定的用户映像。  
   * **AzureRm.Insights**  
         AzureRm 汇总模块现在包括已发布的版本 5.1.5 支持**api-version 2018-01-01**指标，指标定义资源类型。

- **AzureStack 1.7.0**这一项重大更改的释放。 有关的重大更改的详细信息，请参阅 https://aka.ms/azspshmigration170
   * **Azs.Backup.Admin 模块**  
         重大更改：备份更改为基于证书的加密模式。 对对称密钥的支持已弃用。  
   * **Azs.Fabric.Admin Module**  
         `Get-AzsInfrastructureVolume` 已弃用。 使用新 cmdlet `Get-AzsVolume`。  
         `Get-AzsStorageSystem` 已弃用。  使用新 cmdlet `Get-AzsStorageSubSystem`。  
         `Get-AzsStoragePool` 已弃用。 `StorageSubSystem`对象包含容量属性。  
   * **Azs.Compute.Admin 模块**  
         Bug 修复- `Add-AzsPlatformImage`， `Get-AzsPlatformImage`:调用`ConvertTo-PlatformImageObject`仅在成功的路径中。  
         Bug 修复- `Add-AzsVmExtension`， `Get-AzsVmExtension`:仅在成功的路径中调用 ConvertTo VmExtensionObject。  
   * **Azs.Storage.Admin 模块**  
         新的存储配额的 bug 修复-如果没有提供，则使用默认值。

若要查看已更新的模块的引用，请参阅[Azure Stack 模块参考](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0)。

## <a name="fixed-issues"></a>修复的问题

- 修复了在该门户展示了用于创建基于策略的 VPN 网关，这在 Azure Stack 中不受支持的选项。 此选项已从门户中删除。

<!-- 16523695 – IS, ASDK -->
- 修复了在其中从虚拟网络更新 DNS 设置后**使用 Azure Stack DNS**到**自定义 DNS**，实例已不使用新的设置更新。

- <!-- 3235634 – IS, ASDK --> 修复了问题的部署的 Vm 中包含的大小**v2**后缀; 例如， **Standard_A2_v2**，必需指定作为后缀**Standard_A2_v2** (小写字母 v)。 如使用全局 Azure，你现在可以使用**Standard_A2_V2** （大写 V）。

<!-- 2869209 – IS, ASDK --> 
- 修复了问题时使用[添加 AzsPlatformImage cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage)，在你必须使用 **-OsUri**参数作为存储帐户上传到磁盘的 URI。 现在还使用该磁盘的本地路径。

<!--  2795678 – IS, ASDK --> 
- 修复了在使用在门户中的高级 VM 大小 （DS、 Ds_v2、 FS、 FSv2） 创建虚拟机 (Vm) 时生成警告的问题。 标准存储帐户中创建了 VM。 虽然这样做不会影响就功能而言，IOPs 或计费，已修复此警告。

<!-- 1264761 - IS ASDK -->  
- 修复了**运行状况控制器**已生成以下警报的组件。 无法安全地忽略，将发出警报：

    - 警报 #1：
       - 名称：基础结构角色不正常
       - 严重性：警告
       - 组件：运行状况控制器
       - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

    - 警报 #2：
       - 名称：基础结构角色不正常
       - 严重性：警告
       - 组件：运行状况控制器
       - 说明：运行状况控制器故障扫描程序不可用。 这可能会影响运行状况报告和指标。


<!-- 3507629 - IS, ASDK --> 
- 修复了问题，设置下的托管磁盘配额值时[计算配额类型](azure-stack-quota-types.md#compute-quota-types)为 0，它相当于 GiB 的默认值。 零个配额值现在是需要遵守。

<!-- 2724873 - IS --> 
- 修复了问题时使用的 PowerShell cmdlet**开始 AzsScaleUnitNode**或**停止 AzsScaleUnitNode**来管理缩放单位，首次尝试启动或停止的缩放单位可能会失败。

<!-- 2724961- IS ASDK --> 
- 修复了在其中你注册的问题**Microsoft.Insight**订阅设置中的资源提供程序和创建 Windows VM 与来宾 OS 诊断已启用，但不是显示 VM 概述页中的 CPU 百分比图表度量值数据。 数据现在显示正确。

- 修复了在哪些运行**Get-azurestacklog** cmdlet 失败后运行**Test-azurestack**相同的特权终结点 (PEP) 会话中。 现在可以使用同一个执行所在的 PEP 会话**Test-azurestack**。

<!-- bug 3615401, IS -->
- 自动备份计划程序服务会进入已修复的问题已意外禁用状态。 

<!--2850083, IS ASDK -->
- 删除**重置网关**从 Azure Stack 门户中，引发了错误，如果单击此按钮的按钮。 此按钮可用作没有函数在 Azure Stack 中 Azure Stack 有多租户网关而不是专用的 VM 实例为每个租户 VPN 网关，因此已将其删除以防止产生混乱。 

<!-- 3209594, IS ASDK -->
- 删除**有效安全规则**从链接**网络属性**边栏选项卡中的作为此功能不支持在 Azure Stack 中。 无存在的链接提供了支持此功能的印象，但不是起作用。 若要缓解混淆，我们删除链接。

<!-- 3139614 | IS -->
- 修复了在其中后从 OEM，更新应用到 Azure Stack**可用更新**通知不显示在 Azure Stack 管理员门户中。

## <a name="changes"></a>更改

<!-- 3083238 IS -->
- 此项更新中的安全增强功能会导致目录服务角色的备份大小增加。 有关已更新的外部存储位置大小调整指导，请参阅[基础结构备份文档](azure-stack-backup-reference.md#storage-location-sizing)。 因为数据传输大小变大，此项更改会导致需要更长时间才能完成备份。 此项更改会影响集成式系统。 

- 从 2019 年 1 月开始，你可以部署 Kubernetes 群集上 Active Directory 联合身份验证服务 (AD FS) 注册，连接 Azure Stack 戳记 （internet 访问权限是必需的）。 按照说明进行操作[此处](azure-stack-solution-template-kubernetes-cluster-add.md)若要下载新 Kubernetes Marketplace 项。 按照说明进行操作[此处](user/azure-stack-solution-template-kubernetes-adfs.md)部署 Kubernetes 群集。 请注意，该值指示在目标系统 ADD 或 AD FS 的新参数注册。 如果它是 AD FS，新字段都可以输入在其中存储部署证书的 Key Vault 参数。

   请注意，即使使用 AD FS 支持 Kubernetes 群集的部署需要 internet 访问权限。

- 在安装之后更新或修补程序到 Azure Stack，新功能可能会引入需要新权限才能被授予一个或多个标识应用程序。 授予这些权限，还需要对主目录中，管理访问权限，因此它不能自动完成。 例如：

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- 目前在 Azure Stack 中，而无需显式无需下载通过 marketplace 联合扩展已成功部署的扩展插件。 正在删除以下版本的这些扩展。 Azure Stack 操作员现在必须显式联合 Azure Stack marketplace 中的这些扩展：

   | Type                     | 版本        |
   |--------------------------|----------------|
   | DSC                      | 2.19.0.0       |
   | IaaSAntimalware          | 1.4.0.0        |
   | BGInfo                   | 2.1            |
   | VMAccessAgent            | 2.0            |
   | CustomScriptExtension    | 1.8            |
   | MicrosoftMonitoringAgent | 1.0.10900.0    |
   | IaaSDiagnostics          | 1.10.1.1       |
   | VMAccessForLinux         | 1.4.0.0        |
   | CustomScriptForLinux     | 1.5.2.0        |
   | DockerExtension          | 1.1.1606092330 |
   | JsonADDomainExtension    | 1.3            |
   | OSPatchingForLinux       | 2.3.0.1        |
   | WebRole                  | 4.3000.14.0    |

   建议在部署扩展时，是否设置 Azure Stack 用户`autoUpgradeMinorVersion`到**true**。 例如：

   ```json
   "type": "Extension",
           "publisher": "ExtensionPublisher",
           "typeHandlerVersion": "1.2",
           "autoUpgradeMinorVersion": "true"
   ```

- 没有准确地规划 Azure Stack 容量的新的考虑因素。 1901 更新是现在可以创建虚拟机的总数限制。  此限制旨在作为临时以免解决方案不稳定。 进行寻址的更多的 Vm 上的稳定性问题的源，但尚未确定具体的时间表以提高补救。 1901年更新后，现在有了每个服务器限制 60 台虚拟机的总体解决方案限制为 700。  例如，8 服务器的 Azure Stack VM 限制将 480 (8 * 60)。  对于 Azure Stack 解决方案的 12 到 16 服务器限制为 700。 已创建此限制，请记住所有计算资源容量注意事项例如保留的复原能力和 CPU 虚拟与物理运算符想要在标记上维护的比率。 有关详细信息，请参阅的容量规划器的新版本。  
情况已达到虚拟机扩展限制，将作为结果返回以下错误代码：VMsPerScaleUnitLimitExceeded，VMsPerScaleUnitNodeLimitExceeded。 
 

- 计算 API 版本为 2017年-12-01 增加。

- 基础结构备份现在仅公共密钥与需要的证书 (。CER) 备份的数据进行加密。 对称加密密钥支持已弃用从 1901 年开始。 如果基础结构备份配置到 1901年更新之前，加密密钥将保持不变。 必须至少为 2 的详细信息更新具有向后兼容性支持来更新备份设置。 有关详细信息，请参阅[Azure Stack 基础结构备份最佳实践](azure-stack-backup-best-practices.md)。

## <a name="common-vulnerabilities-and-exposures"></a>通用漏洞和披露

此更新安装以下安全更新：  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


有关这些漏洞的详细信息，单击上面的链接，或请参阅 Microsoft 知识库文章[4480977](https://support.microsoft.com/en-us/help/4480977)。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 运行 [Test-AzureStack](azure-stack-diagnostic-test.md) 时，如果 **AzsInfraRoleSummary** 或 **AzsPortalApiSummary** 测试失败，系统会提示你结合 `-Repair` 标志运行 **Test-AzureStack**。  如果运行此命令，则将会失败并出现以下错误消息：  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- 运行 [Test-AzureStack](azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

- <!-- 2468613 - IS -->在安装此更新的过程中，可能会看到标题为 `Error – Template for FaultType UserAccounts.New is missing.` 的警报。可以放心忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](azure-stack-servicing-policy.md)。  

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 – IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- 3557860 - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- 1663805 - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

<!-- ### Health and monitoring -->

### <a name="compute"></a>计算

- 当创建新的 Windows 虚拟机 (VM)，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果 VM 上启用启动诊断，但删除启动诊断存储帐户后，将发生此错误。 若要解决此问题，请重新创建具有您之前从未使用过与同名的存储帐户。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- TBD - IS ASDK --> 
- 更新应用 1901年后，部署包含托管磁盘的 Vm 时，您可能会遇到以下问题：

   - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。
   - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步骤来重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

### <a name="networking"></a>网络  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 门户中，对于已附加到 VM 实例的网络适配器，在更改与其绑定的 IP 配置的静态 IP 地址时，会看到一条警告消息，其中指出 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`。

    可以放心忽略此消息；即使 VM 实例未重启，IP 地址也会更改。

<!-- 3632798 - IS, ASDK -->
- 在门户中，如果添加入站安全规则并选择“服务标记”作为源，“服务标记”列表中会显示多个不适用于 Azure Stack 的选项。 在 Azure Stack 中有效的选项仅限以下几个：

    - **Internet**
    - **VirtualNetwork**
    - **AzureLoadBalancer**
  
    在 Azure Stack 中，不支持将其他选项用作源标记。 同样，如果添加出站安全规则并选择“服务标记”作为目标，则显示与“源标记”相同的选项列表。 仅有的有效选项与“源标记”的有效选项相同，如以上列表中所述。

- 网络安全组 (Nsg) 在相同的方式为全球 Azure 中的 Azure Stack 中无效。 在 Azure 中，您可以在一个 NSG 规则中设置多个端口 (使用门户、 PowerShell 和 Resource Manager 模板)。 Azure Stack 中但不能设置多个端口上通过门户的一个 NSG 规则。 若要解决此问题，使用资源管理器模板或 PowerShell，若要设置这些其他规则。

<!-- 3203799 - IS, ASDK -->
- Azure Stack 不支持 4 个以上的网络接口 (Nic) 现在，附加到 VM 实例而不考虑实例的大小。

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，必须先注册存储资源提供程序。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1901 更新包下载[此处](https://aka.ms/azurestackupdatedownload)。 

只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  

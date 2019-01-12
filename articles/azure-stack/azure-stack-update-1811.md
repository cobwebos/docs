---
title: Azure Stack 1811 更新 |Microsoft Docs
description: 了解有关 Azure Stack 集成系统，包括最新内容、 1811年更新的已知的问题，以及下载更新的位置。
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
ms.date: 01/12/2019
ms.author: sethm
ms.reviewer: adepue
ms.openlocfilehash: 4f5558d17e2f290ed7255350f304ed2057a6d783
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247616"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 更新

*适用于：Azure Stack 集成系统*

本文介绍 1811年更新包的内容。 更新包包括改进、 修复程序和 Azure Stack 的此版本的新功能。 本文还介绍了此版本中的已知的问题，并包含一个链接，以便您可以下载更新。 已知的问题分为与更新过程直接相关的问题和版本 （安装后） 的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1811 更新内部版本号是**1.1811.0.101**。

## <a name="hotfixes"></a>修补程序

Azure Stack 释放定期修补程序。 请务必安装[最新的 Azure Stack 修补程序](#azure-stack-hotfixes)为 1809 到 1811年更新 Azure Stack 之前。

> [!TIP]  
> 订阅以下*RSS*或*Atom*源，以保持与 Azure Stack 修补程序：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1809**:[KB 4481548 – Azure Stack 修补程序 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**:没有当前可用的修复程序。

## <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 在安装期间 1811年更新，必须确保管理员门户中的所有实例都已都关闭。 用户门户可以保持打开状态，但在管理门户，必须关闭。

- 获取 Azure Stack 部署的 Azure Stack 扩展主机准备就绪。 准备您的系统使用以下指南：[准备适用于 Azure Stack 扩展主机](azure-stack-extension-host-prepare.md)。 
 
- 安装[最新的 Azure Stack 修补程序](#azure-stack-hotfixes)为 1809 之前更新到 1811年。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    如果没有扩展主机要求满足，`Test-AzureStack`输出会显示以下消息： 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Azure Stack 1811 更新需要，正确的必需扩展主机证书导入 Azure Stack 环境。 若要继续 1811年更新的安装，必须导入扩展主机所需的 SSL 证书。 若要导入证书，请参阅[本节](azure-stack-extension-host-prepare.md#import-extension-host-certificates)。

    如果忽略每个警告，并仍选择安装 1811年更新，则更新将失败在大约 1 小时，并显示以下消息：   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    之后已正确导入的必需扩展主机证书，您可以恢复 1811年更新在管理员门户中。 Azure Stack 操作员在更新过程中计划维护时段，Microsoft 建议，而失败的原因是缺少扩展主机证书不应影响现有工作负荷或服务。  

    安装过程中的此更新，在 Azure Stack 用户门户将不可用在配置扩展主机时。 扩展主机的配置可能需要最多 5 个小时。 在此期间，你可以检查更新状态，或者使用 [Azure Stack 管理员 PowerShell 或特权终结点](azure-stack-monitor-update.md)继续执行某个失败的更新安装。

## <a name="new-features"></a>新增功能

此更新包括以下新功能和改进适用于 Azure Stack:

- 此版本中，[扩展主机](azure-stack-extension-host-prepare.md)已启用。 扩展主机可以简化网络集成，并改善 Azure Stack 的安全状况。

- 添加了支持设备身份验证与 Active Directory 联合身份验证服务 (AD FS)，特别是使用 Azure CLI 时。 有关详细信息，请参阅[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](./user/azure-stack-version-profiles-azurecli2.md)

- 添加了的对服务主体与 Active Directory 联合身份验证服务 (AD FS) 使用客户端机密。 有关详细信息，请参阅[为 AD FS 创建服务主体](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)。

- 此版本添加了对以下 Azure 存储服务 API 版本的支持：**2017-07-29**， **2017年-11-09**。 此外添加了以下 Azure 存储资源提供程序 API 版本的支持：**2016-05-01**， **2016年-12-01**， **2017年-06-01**，并且**2017年-10-01**。 有关详细信息，请参阅 [Azure Stack 存储：差异和注意事项](./user/azure-stack-acs-differences.md)。

- 添加了新特权终结点命令更新和删除 ADFS 服务主体。 有关详细信息，请参阅[为 AD FS 创建服务主体](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)。

- 添加了新的缩放单位节点操作，使 Azure Stack 操作员可以启动，请停止和关闭的情况下缩放单位节点。 有关详细信息，请参阅[Azure Stack 中的缩放单位节点操作](azure-stack-node-actions.md)。

- 添加一个新区域属性边栏选项卡，显示环境的注册详细信息。 可以通过单击查看此信息**区域管理**在管理员门户中，在默认仪表板上磁贴，然后选择**属性**。

- 添加了一个新的特权终结点命令来更新 BMC 凭据使用用户名和密码，用于与物理计算机进行通信。 有关详细信息，请参阅[更新基板管理控制器\(BMC) 凭据](azure-stack-rotate-secrets.md)。

- 中的管理员和用户门户，它是在 Azure 门户中可用的方式类似的右上角添加但访问 Azure 路线图的功能的帮助和支持的图标 （问号）。

- 添加为断开连接的用户体验改进的 Marketplace 管理。 若要在连接断开的环境中发布 Marketplace 项上传过程简化为一个步骤中，而不是单独上传映像，并在 Marketplace 包。 已上传的产品还将显示在 Marketplace 管理边栏选项卡。 有关详细信息，请参阅[此部分](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher)。 

- 此版本通过添加轮换期间仅外部证书的功能减少的所需的维护时段为机密轮换[Azure Stack 机密轮换](azure-stack-rotate-secrets.md)。

- [Azure Stack PowerShell](azure-stack-powershell-install.md)已更新到版本 1.6.0。 更新 Azure Stack 中包括新存储相关功能的支持。 有关详细信息，请参阅发行说明[Azure Stack 管理模块在 PowerShell 库中的 1.6.0](https://www.powershellgallery.com/packages/AzureStack/1.6.0)更新或安装 Azure Stack PowerShell 的信息，请参阅[安装适用于 PowerShellAzure Stack](azure-stack-powershell-install.md)。

- 当使用 Azure Stack 门户创建虚拟机时，默认情况下现在启用托管的磁盘。 请参阅[已知问题](#known-issues-post-installation)部分，了解托管磁盘，以避免虚拟机创建失败问题所需的其他步骤。

- 此版本引入了警报**修复**针对 Azure Stack 操作员的操作。 1811 中的某些警报提供**修复**，可以选择要解决此问题的警报中的按钮。 有关详细信息，请参阅[Azure Stack 中监视运行状况和警报](azure-stack-monitor-health.md)。

## <a name="fixed-issues"></a>修复的问题

<!-- TBD - IS ASDK --> 
- 修复了公共 IP 地址使用计量数据显示相同的问题**EventDateTime**而不是每个记录的值**TimeDate**演示创建记录时的时间戳。 现在可以使用此数据执行的公共 IP 地址用量的准确计帐。

<!-- 3099544 – IS, ASDK --> 
- 修复了创建新虚拟机 (VM) 使用 Azure Stack 门户时出现的问题。 选择 VM 大小时导致**美元/月**列以显示**不可用**消息。 此列不会再出现;显示 VM 定价的列不支持在 Azure Stack 中。

<!-- 2930718 - IS ASDK --> 
- 在其中修复了管理员门户中，访问任何用户订阅的详细信息后关闭该边栏选项卡，并单击时**最近**，不显示用户订阅名称。 用户订阅名称现在出现。

<!-- 3060156 - IS ASDK --> 
- 修复了门户管理员和用户门户： 单击门户设置并选择**删除所有设置和专用仪表板**未无法按预期工作，并显示错误通知的。 此选项现在可以正常工作。

<!-- 2930799 - IS ASDK --> 
- 修复了门户管理员和用户门户： 下**所有服务**，该资产**DDoS 防护计划**错误地列出。 它在 Azure Stack 中不可用。 列表已被删除。
 
<!--2760466 – IS  ASDK --> 
- 修复了安装新的 Azure Stack 环境时出现的问题中的警报，该值指示**需要激活**未显示。 它现在正确显示。

<!--1236441 – IS  ASDK --> 
- 修复了阻止使用 ADFS 时对用户组应用 RBAC 策略问题。

<!--3463840 - IS, ASDK --> 
- 修复了使用基础结构备份时由于无法访问文件服务器从公共 VIP 网络而失败。 这一修补将移动基础结构备份服务返回到公共基础结构网络。 如果应用最新[1809年的 Azure Stack 修补程序](#azure-stack-hotfixes)解决此问题，1811年更新将不进行任何进一步的修改。 

<!-- 2967387 – IS, ASDK --> 
- 修复了在用来登录到 Azure Stack 管理员或用户门户的帐户显示为**无法识别的用户**。 显示了此消息，如果帐户不具有任一**第一个**或**最后一个**指定名称。   

<!--  2873083 - IS ASDK --> 
- 修复了在哪些使用门户创建虚拟机规模集 (VMSS) 导致**实例大小**下拉列表中未正确加载时使用的 Internet Explorer。 此浏览器现在可正常工作。  

<!-- 3190553 - IS ASDK -->
- 修复了生成的该值指示基础结构角色实例是不可用或缩放单位节点已脱机的干扰性警报的问题。

## <a name="changes"></a>更改

- 若要查看和编辑配额在计划中的新方法是在 1811年中引入的。 有关详细信息，请参阅[查看现有配额](azure-stack-quota-types.md#view-an-existing-quota)。

<!-- 3083238 IS -->
- 此更新中的安全增强功能会导致目录服务角色的备份大小增加。 有关更新大小调整的外部存储位置的指南，请参阅[基础结构备份文档](azure-stack-backup-reference.md#storage-location-sizing)。 此更改会导致较长时间才能完成由于较大的大小数据传输的备份。 此更改会影响集成的系统。 

- 1811，从 Get AzsCsvsRecoveryKeys 到 Get AzsRecoveryKeys 中重命名现有的 PEP cmdlet 来检索 BitLocker 恢复密钥。 有关如何检索 BitLocker 恢复密钥的详细信息，请参阅[说明如何检索密钥](azure-stack-security-bitlocker.md)。

## <a name="common-vulnerabilities-and-exposures"></a>通用漏洞和披露

此更新安装以下安全更新：  

- [CVE 2018 8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE 2018 8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE 2018 8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE 2018 8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE 2018 8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE 2018 8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE 2018 8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE 2018 8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE 2018 8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE 2018 8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE 2018 8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE 2018 8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE 2018 8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE 2018 8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE 2018 8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE 2018 8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE 2018 8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE 2018 8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE 2018 8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)


有关这些漏洞的详细信息，单击上面的链接，或请参阅 Microsoft 知识库文章[4467684](https://support.microsoft.com/help/4467684)。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 安装过程中的 1811年更新，请确保在此期间关闭了管理员门户中的所有实例。 用户门户可以保持打开状态，但在管理门户，必须关闭。

- 运行时[Test-azurestack](azure-stack-diagnostic-test.md)，如果**AzsInfraRoleSummary**或**AzsPortalApiSummary**测试失败，则系统会提示您运行**Test-azurestack**与`-Repair`标志。  如果运行此命令，则将会失败并出现以下错误消息：`Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  将在未来的版本中解决此问题。

- 1811 更新的安装期间，Azure Stack 使用门户将不可用在配置扩展主机时。 扩展主机的配置可能需要最多 5 个小时。 在此期间，你可以检查更新状态，或者使用 [Azure Stack 管理员 PowerShell 或特权终结点](azure-stack-monitor-update.md)继续执行某个失败的更新安装。 

- 在安装期间的 1811年更新，用户门户仪表板可能不可用，并自定义项可能会丢失。 您可以还原到默认设置，通过打开门户设置并选择完成更新后的仪表板**还原默认设置**。

- 在运行时[Test-azurestack](azure-stack-diagnostic-test.md)，显示一条警告消息从基板管理控制器 (BMC)。 可以放心地忽略此警告。

- <!-- 2468613 - IS --> 此更新的安装期间，可能会看到警报标题`Error – Template for FaultType UserAccounts.New is missing.`可以放心地忽略这些警报。 此更新在安装完成后，会自动关闭，将发出警报。

- <!-- 3139614 | IS --> 如果已从您的 OEM，对 Azure Stack 应用更新**可用更新**通知可能不会显示在 Azure Stack 管理员门户中。 若要安装 Microsoft 更新，请下载并导入它使用位于此处的说明手动[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。

## <a name="post-update-steps"></a>更新后步骤

- 此更新的安装之后, 安装任何适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)，以及我们[服务策略](azure-stack-servicing-policy.md)。  

- 检索数据的加密密钥并安全地将其存储在 Azure Stack 部署之外。 请按照[说明如何检索密钥](azure-stack-security-bitlocker.md)。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 – IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- TBD - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决此问题，首先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 作为一种解决方法，使用[PowerShell 验证权限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

### <a name="health-and-monitoring"></a>运行状况和监视

<!-- 1264761 - IS ASDK -->  
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  

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

    可以放心地忽略这两个警报。 它们将随着时间的推移自动关闭。  

### <a name="compute"></a>计算

- 当创建新 Windows 虚拟机 (VM)，**设置**边栏选项卡要求才能继续选择公共入站的端口。 在 1811，此设置是必需的但不起作用。 这是因为该功能取决于 Azure 防火墙，这会在 Azure Stack 中未实现。 可以选择**无公共入站端口**，或任何其他选项以继续进行创建 VM。 该设置不起。

- 当创建新的 Windows 虚拟机 (VM)，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果 VM 上启用启动诊断，但删除启动诊断存储帐户后，将发生此错误。 若要解决此问题，请重新创建具有您之前从未使用过与同名的存储帐户。

- 创建时[Dv2 系列 VM](./user/azure-stack-vm-considerations.md#virtual-machine-sizes)，D11 14v2 Vm，您可以分别创建 4、 8、 16 和 32 个数据磁盘。 但是，创建 VM 窗格将显示 8、 16、 32 和 64 个数据磁盘。

<!-- 3235634 – IS, ASDK -->
- 若要将 Vm 部署包含的大小**v2**后缀; 例如， **Standard_A2_v2**，指定作为后缀**Standard_A2_v2** (小写 v)。 请勿使用 **Standard_A2_V2**（大写 V）。 这适用于全球 Azure，在 Azure Stack 上有不一致的问题。

<!-- 2869209 – IS, ASDK --> 
- 使用 [**Add-AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则此 cmdlet 会失败并显示以下错误： 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- 当你使用门户创建虚拟机 (Vm) 高级 VM 大小 （DS、 Ds_v2、 FS、 FSv2） 中时，在标准存储帐户中创建 VM。 在标准存储帐户中创建不影响功能、IOPS 或计费。 可以放心忽略带有以下字样的警告： 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像不是 Azure Stack 上可用，请选择另一个适用于你的部署，或使用指定在从应用商店部署之前已下载的另一个 CentOS 映像的 Azure 资源管理器模板运算符。  

<!-- 2724873 - IS --> 
- 使用 PowerShell cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 管理缩放单元时，首次尝试启动或停止缩放单元可能会失败。 如果 cmdlet 在第一次运行时失败，请再次运行 cmdlet。 第二次运行应成功完成该操作。 

<!-- TBD - IS ASDK --> 
- 如果预配某个扩展在 VM 部署时间太长，让预配超时，而不是尝试停止该进程来解除分配或删除该 VM。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

<!-- 2724961- IS ASDK --> 
- 注册时**Microsoft.Insight**资源提供程序中的订阅设置，并创建具有来宾 OS 诊断已启用的 Windows VM，VM 概述页中的 CPU 百分比图表不显示指标数据。

   若要查找指标数据，例如 VM 的 CPU 百分比图表，请转到**指标**窗口并显示所有受支持的 Windows VM 来宾指标。

<!-- 3507629 - IS, ASDK --> 
- 托管磁盘创建了两个新的[计算配额类型](azure-stack-quota-types.md#compute-quota-types)来限制可以预配的托管磁盘的最大容量。 默认情况下将为每个托管磁盘配额类型分配 2048 GiB。 不过，你可能会遇到以下问题：

   - 对于在 1808 更新之前创建的配额，托管磁盘配额在管理门户中将显示为值 0，虽然分配了 2048 GiB。 你可以根据实际需求增大或减小该值，新设置的配额值将替代 2048 GiB 默认值。
   - 如果将配额值更新为 0，则它等效于默认值 2048 GiB。 作为一种解决方法，请将配额值设置为 1。

<!-- TBD - IS ASDK --> 
- 更新后应用 1811年，部署包含托管磁盘的 Vm 时，您可能会遇到以下问题：

   - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 选择**资源提供程序**，然后选择**Microsoft.Compute**，然后单击**重新注册**。
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。
   - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步骤来重新配置每个来宾目录。

- 创建具有 SSH 授权启用的 Ubuntu 18.04 VM 将不允许要使用 SSH 密钥登录。 解决方法是，使用 VM 访问 for Linux 扩展预配后，实现 SSH 密钥或使用基于密码的身份验证。

### <a name="networking"></a>网络  

<!-- 1766332 - IS ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 - IS ASDK --> 
- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 创建第一个的本地网络网关连接，后续尝试使用相同的 IP 地址创建本地网络网关资源后将被拒绝。

<!-- 16309153 - IS ASDK --> 
- 已创建的 DNS 服务器设置的虚拟网络上**自动**，更改为自定义 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack 期间*机密轮换*，一段中的公共 IP 地址是无法访问为 2 到 5 分钟内。

<!-- 2664148 - IS ASDK --> 
-   在其中，租户通过使用 S2S VPN 隧道访问虚拟机的情况下，它们可能会遇到其中连接尝试失败，如果已创建网关后，在本地子网已添加到本地网络网关的方案。 

- 在 Azure Stack 门户中，当更改绑定到附加到 VM 实例，一个网络适配器的 IP 配置的静态 IP 地址时您将看到一条警告消息，指出 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`。 

    您可以放心地忽略此消息;将更改的 IP 地址，即使 VM 实例不会重启。

- 在门户中，在**网络属性**边栏选项卡中不存在用于链接**有效安全规则**每个网络适配器。 如果选择此链接，新的边栏选项卡打开，并显示错误消息`Not Found.`发生此错误的原因尚不支持 Azure Stack**有效安全规则**。

- 在门户中，如果添加入站的安全规则并选择**服务标记**作为源，几个选项会显示在**源标记**不适用于 Azure Stack 的列表。 在 Azure Stack 中有效的唯一选项是按如下所示：

    - **Internet**
    - **VirtualNetwork**
    - **AzureLoadBalancer**
  
    在 Azure Stack 中的源标记为不支持其他选项。 同样，如果您添加出站安全规则，并选择**服务标记**作为目标，相同的选项列表**源标记**显示。 仅有效选项为相同**源标记**，如前面的列表中所述。

- **新建 AzureRmIpSecPolicy** PowerShell cmdlet 不支持设置**DHGroup24**为`DHGroup`参数。

### <a name="infrastructure-backup"></a>基础结构备份

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- 启用自动备份之后, 计划程序服务将进入禁用状态意外。 自动备份将被禁用，会发出警告在管理员门户中的，将检测备份控制器服务。 禁用自动备份时，此警告在预料之中。 
    - 原因：此问题的原因会导致丢失的计划程序配置的服务中的 bug。 此 bug 不会更改存储位置、 用户名、 密码或加密密钥。   
    - 补救措施：若要缓解此问题，请在基础结构备份资源提供程序中打开备份控制器设置边栏选项卡，然后选择**启用自动备份**。 请确保设置所需的频率和保留期。
    - 匹配项：低 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，必须注册存储资源提供程序。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1811 更新包下载[此处](https://aka.ms/azurestackupdatedownload)。 

在连接的方案，Azure Stack 部署定期检查受保护的终结点，并自动通知你是否可以更新，可用于你的云。 有关详细信息，请参阅[适用于 Azure Stack 中管理更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>后续步骤

- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。  

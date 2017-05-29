---
title: "将备份保管库升级到恢复服务保管库（预览）| Microsoft Docs"
description: "用于将 Azure 备份保管库升级到恢复服务保管库的命令和支持信息。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/10/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 00744d70a7644d80cfd99acbcf96966574f4512e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>将备份保管库升级到恢复服务保管库

本文介绍如何将备份保管库升级到恢复服务保管库。 升级过程不会影响任何正在运行的备份作业，并且不会丢失备份数据。 将备份保管库升级到恢复服务保管库的主要原因包括：
- 备份保管库的所有功能保留在恢复服务保管库中。
- 恢复服务保管库的功能比备份保管库更多，包括更高的安全性、集成式监视、更快的还原速度和项级还原，以及从改进的简化门户管理备份项。
-  新功能只会针对恢复服务保管库发布。

## <a name="impact-to-operations-during-upgrade"></a>升级过程中对操作的影响

将备份保管库升级到恢复服务保管库时，数据平面操作不会受到任何影响。 所有备份作业可如常继续，任何活动的还原作业也可继续而不会中断。 升级期间，管理操作会出现短暂的停止，并且你无法保护新项或创建临时备份作业。 升级期间，对 IaaS VM 执行的还原作业不会运行。 <MG>：正常停止的活动还原作业与 IaaS VM 还原作业之间有什么区别？
保管库升级在不到一小时之内即可完成。 完成后，恢复服务保管库将取代备份保管库。

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>自动化与工具在升级之后的变化

在为保管库升级准备基础结构时，必须更新现有的自动化或工具，确保它在升级后可继续运行。
请参阅 [Service Manager 部署模型](backup-client-automation-classic.md)和 [Resource Manager 部署模型](backup-client-automation.md)的 PowerShell cmdlet 参考


## <a name="before-you-upgrade"></a>升级之前

将备份保管库升级到恢复服务保管库之前，请检查以下问题。

- **最低代理版本**：若要升级保管库，请确保 Microsoft Azure 恢复服务 (MARS) 代理的版本至少为 2.0.9070.0。 如果 MARS 代理版本低于 2.0.9070.0，请在启动升级过程之前更新该代理。
- **基于实例的计费模型**：恢复服务保管库仅支持基于实例的计费模型。 如果备份保管库使用旧式基于存储的计费模型，请在升级期间转换计费模型。
- **没有正在进行的备份配置操作**：升级期间，对管理平面的访问将受到限制。 请先完成所有管理平面操作，然后开始升级。

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>使用 PowerShell 脚本升级保管库

可以使用 PowerShell 脚本将备份保管库升级到恢复服务保管库。 检查是否准备好了所需的 PowerShell 组件用于触发保管库升级。 适用于备份保管库的 PowerShell 脚本不适用于恢复服务保管库。 准备环境以升级保管库：

1. 安装或升级到 [Windows Management Framework (WMF) 版本 5](https://www.microsoft.com/download/details.aspx?id=50395) 或更高版本。
2. 下载并[安装 Azure PowerShell 3.7.0](https://github.com/Azure/azure-powershell/releases/download/v3.7.0-March2017/azure-powershell.3.7.0.msi)。
3. 更新 PowerShell 模块：
  - 对于 Windows Server：
    - Install-Module -Name Azure -RequiredVersion 3.8.0
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0
  - 适用于 Windows 10
    - Install-Module -Name Azure -RequiredVersion 3.8.0 -AllowClobber
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0 -AllowClobber
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0 -AllowClobber
4. 下载 [PowerShell 脚本](http://download.microsoft.com/download/1/c/6/1c6ed72e-38f9-4675-9cf9-9b8a06da7cd3/recoveryservicesvaultupgrade.ps1)以升级保管库。

### <a name="run-the-powershell-script"></a>运行 PowerShell 脚本

使用以下脚本升级保管库。 以下示例脚本包含参数说明。

RecoveryServicesVaultUpgrade.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`


SubscriptionID - 正在升级的保管库的订阅 ID 编号。
VaultName - 正在升级的备份保管库的名称。
位置 - 正在升级的保管库的位置。
ResourceType - 使用 BackupVault。
TargetResourceGroupName - 由于要将保管库升级到基于 Resource Manager 的部署，因此请指定资源组。 可以使用现有的资源组，也可以通过提供新名称创建新的资源组。 如果资源组的名称拼写不当，则会创建新的资源组。 若要详细了解资源组，请阅读[有关资源组的概述](../azure-resource-manager/resource-group-overview.md#resource-groups)文章。

>[!NOTE]
> 资源组名称具有约束。 请务必遵循指导；否则可能导致保管库升级失败。
>
>

以下代码片段是 PowerShell 命令大致形式的示例：

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

也可以不结合任何参数运行该脚本，在这种情况下，系统将要求提供所有必需参数的输入。

PowerShell 脚本将提示输入凭据。 请输入凭据两次：一次是输入 Service Manager 帐户的凭据，另一次是输入 Resource Manager 帐户的凭据。

### <a name="pre-requisites-checking"></a>先决条件检查
输入 Azure 凭据后，Azure 将检查环境是否满足以下先决条件：

- **最低代理版本** - 将备份保管库升级到恢复服务保管库要求 MARS 代理版本至少为 2.0.9070。 如果使用版本低于 2.0.9070 的代理将项注册到了备份保管库，先决条件检查将会失败。 如果先决条件检查失败，请更新代理，然后重试升级保管库。 可以从 [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) 下载最新版本的代理。
- **正在进行的配置作业**：如果某人正在针对设置为要升级的备份保管库配置作业，或者正在注册某个项，先决条件检查将会失败。 请先完成配置或完成项的注册，然后再启动保管库升级过程。
- **基于存储的计费模型**：恢复服务保管库支持基于实例的计费模型。 如果在使用基于存储的计费模型的备份保管库上运行保管库升级，系统将提示你连同保管库一起升级计费模型。 否则，可以先更新计费模型，然后再运行保管库升级。
- 标识恢复服务保管库的资源组。 若要利用 Resource Manager 部署功能，必须在资源组中放置一个恢复服务保管库。 如果不知道要使用哪个资源组，请提供一个名称，然后，升级过程将会创建该资源组。 升级过程还会将保管库与新资源组相关联。

升级过程完成检查先决条件后，会提示你要开始保管库升级。 确认后，升级过程通常需要花费大约 15 到 20 分钟才能完成，具体时间取决于保管库的大小。 如果保管库较大，升级可以需要长达 90 分钟。

## <a name="managing-your-recovery-services-vaults"></a>管理恢复服务保管库

以下屏幕显示了 Azure 门户中从备份保管库升级的新恢复服务保管库。 第一个屏幕显示保管库仪表板，其中显示了保管库的关键实体。

![从备份保管库升级的恢复服务保管库示例](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

第二个屏幕显示可帮助你开始使用恢复服务保管库的帮助链接。

![“快速启动”边栏选项卡中的帮助链接](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="frequently-asked-questions"></a>常见问题

**升级计划是否影响正在进行的备份？**</br>
否。 升级期间和之后，正在进行的备份都会继续，而不会中断。

**如果不尽快规划升级，保管库会发生什么情况？**</br>
由于所有新功能都是针对恢复服务保管库计划的，并且经典门户将被弃用，因此，Microsoft 将会弃用备份保管库和 Service Manager 模型。 将来，Microsoft 会触发将所有保管库自动升级到恢复服务保管库的过程。 到时，客户可以控制何时升级其保管库。

**对于现有的工具而言，此升级计划有何意义？**</br>  
将工具升级到恢复服务保管库所基于的 Resource Manager 部署模型，是必须在升级计划中考虑的最重要更改之一。

**停机时间有多长？**</br>
这取决于要升级的资源数量。 对于小型部署（几十个受保护的实例），整个升级过程应会不到 20 分钟内完成。 对于大型部署，最长需要一小时。

**升级后是否可以回滚？**</br>
否。 成功升级资源后，就不支持回滚。

**是否可以验证订阅或资源，确定它们是否可升级？**</br>
是的。 升级过程的第一个步骤是验证资源是否可升级。 如果先决条件验证失败，你会收到消息，其中包含无法完成升级的所有原因。

**应有哪些权限才能触发保管库升级？**</br>
若要执行保管库升级，必须在 Azure 经典门户中将你添加为订阅的协同管理员。 即使你已在 Azure 门户中被添加为所有者，也仍然需要执行此操作。 请尝试在 Azure 经典门户中添加订阅的协同管理员，确定你是否为订阅的协同管理员。 如果无法添加协同管理员，请联系订阅的服务管理员或协同管理员，以将自己添加为协同管理员。

**是否可以升级基于 CSP 的备份保管库？**</br>
否。 目前无法升级基于 CSP 的备份保管库。 我们将在后续版本中添加此项支持。

**升级后是否可以查看经典保管库？**</br>
否。 升级后无法查看或管理经典保管库。 只能使用新式 Azure 门户针对保管库执行所有管理操作。

**升级失败，并且指出需要在计算机上更新不再存在的代理。对于这种情况，该怎么办？**</br>
如果需要存储此计算机的备份以便长期保留，则无法升级保管库。 我们将在后续版本中添加此项支持，以便能够升级此类保管库。
如果不再需要存储此计算机的备份，请从保管库中注销此计算机，然后重试升级。

**升级后为何看不到本地资源的作业信息**</br>
本地备份监视（MARS 代理、DPM 和 MABS）是将备份保管库升级到恢复服务保管库后获得一项新功能。 监视信息最长需要 12 小时才能与服务同步。

**如何报告问题？**</br>
如果在升级过程中遇到任何失败，请记下错误中列出的 OperationId。 Microsoft 支持部门将会尽力主动解决此问题。 你可以联系支持部门，或者向 rsvaultupgrade@service.microsoft.com 发送电子邮件并提供订阅 ID、保管库名称和操作 ID。 我们将努力尽快解决此问题。 除非 Microsoft 明确指示，否则请不要重试操作。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：</br>
[备份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)</br>
[备份 Windows Server](backup-configure-vault.md)


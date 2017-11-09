---
title: "将备份保管库升级到恢复服务保管库（预览）| Microsoft Docs"
description: "将 Azure 备份保管库升级到恢复服务保管库的说明和支持信息。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/03/2017
ms.author: sogup;markgal;arunak
ms.openlocfilehash: c7eb4514dca806d6e6470091423785b30a7d4bcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>将备份保管库升级到恢复服务保管库

本文介绍如何将备份保管库升级到恢复服务保管库。 升级过程不会影响任何正在运行的备份作业，并且不会丢失备份数据。 将备份保管库升级到恢复服务保管库的主要原因包括：
- 备份保管库的所有功能保留在恢复服务保管库中。
- 恢复服务保管库的功能比备份保管库更多，包括更高的安全性、集成式监视、更快的还原速度和项级还原。
- 从改进的简化门户管理备份项。
- 新功能仅适用于恢复服务保管库。

## <a name="impact-to-operations-during-upgrade"></a>升级过程中对操作的影响

将备份保管库升级到恢复服务保管库时，数据平面操作不会受到任何影响。 所有备份作业可如常继续，任何活动的还原作业也可继续而不会中断。 升级期间，管理操作会导致短暂停机，无法保护新项或创建临时备份作业。 升级期间，对 IaaS VM 执行的还原作业不会运行。 保管库升级在不到一小时之内即可完成。 完成后，恢复服务保管库将取代备份保管库。

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>自动化与工具在升级之后的变化

在为保管库升级准备基础结构时，必须更新现有的自动化或工具，确保它在升级后可继续运行。
请参阅 [Service Manager 部署模型](backup-client-automation-classic.md)和[资源管理器部署模型](backup-client-automation.md)的 PowerShell cmdlet 参考。


## <a name="before-you-upgrade"></a>升级之前

将备份保管库升级到恢复服务保管库之前，请检查以下问题。

- 最低代理版本：若要升级保管库，请确保 Microsoft Azure 恢复服务 (MARS) 代理的版本至少为 2.0.9083.0。 如果 MARS 代理版本低于 2.0.9083.0，请在启动升级过程之前更新该代理。
- **基于实例的计费模型**：恢复服务保管库仅支持基于实例的计费模型。 如果备份保管库使用旧式基于存储的计费模型，请在升级期间转换计费模型。
- **没有正在进行的备份配置操作**：升级期间，对管理平面的访问将受到限制。 完成所有管理平面操作，然后开始升级。

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>使用 PowerShell 脚本升级保管库

可以使用 PowerShell 脚本将备份保管库升级到恢复服务保管库。 检查是否准备好了所需的 PowerShell 组件用于触发保管库升级。 适用于备份保管库的 PowerShell 脚本不适用于恢复服务保管库。 准备环境以升级保管库：

1. 安装或升级到 [Windows Management Framework (WMF) 版本 5](https://www.microsoft.com/download/details.aspx?id=50395) 或更高版本。
2. [安装 Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi)。
3. 下载 [PowerShell 脚本](https://aka.ms/vaultupgradescript2)以升级保管库。

### <a name="run-the-powershell-script"></a>运行 PowerShell 脚本

使用以下脚本升级保管库。 以下示例脚本包含参数说明。

RecoveryServicesVaultUpgrade-1.0.2.ps1 -SubscriptionID `<subscriptionID>` -VaultName `<vaultname>` -Location `<location>` -ResourceType `BackupVault` -TargetResourceGroupName `<rgname>`

SubscriptionID - 正在升级的保管库的订阅 ID 编号。<br/>
VaultName - 正在升级的备份保管库的名称。<br/>
位置 - 正在升级的保管库的位置。<br/>
ResourceType - 使用 BackupVault。<br/>
TargetResourceGroupName - 由于要将保管库升级到基于资源管理器的部署，因此请指定资源组。 可以使用现有的资源组，也可以通过提供新名称创建新的资源组。 如果资源组的名称拼写不当，则会创建新的资源组。 若要详细了解资源组，请阅读[有关资源组的概述](../azure-resource-manager/resource-group-overview.md#resource-groups)文章。

>[!NOTE]
> 资源组名称具有约束。 请务必遵循指导；否则可能导致保管库升级失败。
>
>**Azure 美国政府**客户运行脚本时需要将环境设置为“AzureUSGovernment”。
>**Azure 中国**客户运行脚本时需要将环境设置为“AzureChinaCloud”。

以下代码片段是 PowerShell 命令大致形式的示例：

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

也可以不结合任何参数运行该脚本，在这种情况下，系统将要求提供所有必需参数的输入。

PowerShell 脚本会提示输入凭据。 请输入凭据两次：一次是输入 Service Manager 帐户的凭据，另一次是输入 Resource Manager 帐户的凭据。

### <a name="pre-requisites-checking"></a>先决条件检查
输入 Azure 凭据后，Azure 会检查环境是否满足以下先决条件：

- 最低代理版本 - 将备份保管库升级到恢复服务保管库要求 MARS 代理版本至少为 2.0.9083.0。 如果使用版本低于 2.0.9083.0 的代理将项注册到了备份保管库，先决条件检查会失败。 如果先决条件检查失败，请更新代理，并重试升级保管库。 可以从 [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) 下载最新版本的代理。
- **正在进行的配置作业**：如果某人正在针对设置为要升级的备份保管库配置作业，或者正在注册某个项，先决条件检查会失败。 请先完成配置或完成项的注册，再启动保管库升级过程。
- **基于存储的计费模型**：恢复服务保管库支持基于实例的计费模型。 如果在使用基于存储的计费模型的备份保管库上运行保管库升级，系统会提示连同保管库一起升级计费模型。 否则，可以先更新计费模型，再运行保管库升级。
- 标识恢复服务保管库的资源组。 若要利用 Resource Manager 部署功能，必须在资源组中放置一个恢复服务保管库。 如果不知道要使用哪个资源组，请提供一个名称，然后，升级过程会创建该资源组。 升级过程还会将保管库与新资源组相关联。

升级过程完成检查先决条件后，会提示要开始保管库升级。 确认后，升级过程通常需要花费大约 15 到 20 分钟才能完成，具体时间取决于保管库的大小。 如果保管库较大，升级可以需要长达 90 分钟。

## <a name="managing-your-recovery-services-vaults"></a>管理恢复服务保管库

以下屏幕显示了 Azure 门户中从备份保管库升级的新恢复服务保管库。 第一个屏幕显示保管库仪表板，其中显示了保管库的关键实体。

![从备份保管库升级的恢复服务保管库示例](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

第二个屏幕显示可助你开始使用恢复服务保管库的帮助链接。

![“快速启动”边栏选项卡中的帮助链接](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>升级后的步骤
恢复服务保管库支持在备份策略中指定时区信息。 成功升级保管库后，转到保管库设置菜单中的备份策略，并更新保管库中配置的每个策略的时区信息。 此屏幕已显示根据创建策略时所用的每个本地时区指定的备份计划时间。 

## <a name="enhanced-security"></a>增强的安全性

备份保管库升级到恢复服务保管库后，将自动启用该保管库的安全设置。 启用安全设置后，某些操作（如删除备份或更改密码）需要 [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md) PIN。 关于增强的安全性的详细信息，请参阅[用于保护混合备份的安全性功能](backup-azure-security-feature.md)一文。 

启用增强的安全性后，数据将在恢复点信息从保管库中删除之后至多保留 14 天。 为此安全数据的存储对客户计费。 安全数据保留期适用于针对 Azure 备份代理、Azure 备份服务器和 System Center Data Protection Manager 的恢复点。 

## <a name="gather-data-on-your-vault"></a>收集保管库中的数据

升级到恢复服务保管库后，请配置 Azure 备份报表（适用于 IaaS VM 和 Microsoft Azure 恢复服务 (MARS)）并使用 Power BI 访问报表。 有关收集数据的其他信息，请参阅文章[配置 Azure 备份报表](backup-azure-configure-reports.md)。

## <a name="frequently-asked-questions"></a>常见问题

**升级计划是否影响正在进行的备份？**</br>
否。 升级期间和之后，正在进行的备份都会继续，而不会中断。

如果不尽快规划升级，保管库会发生什么情况？</br>
由于所有新功能仅适用于恢复服务保管库，我们强烈建议升级保管库。 Microsoft 最终将弃用经典门户。 自 2017 年 9 月 1 日起，Microsoft 会将备份保管库自动升级为恢复服务保管库。 2017 年 11 月 1 日之前，Microsoft 将完成升级过程。 保管库可在 9 月或 10 月中的任何时间自动升级。 Microsoft 建议尽快升级保管库。

对于现有的工具而言，此升级有何意义？</br>
将工具更新为资源管理器部署模型。 恢复服务保管库是为在资源管理器部署模型使用而创建的。 规划资源管理器部署模型以及记录保管库中的差异十分重要。 

升级期间，是否会出现停机？</br>
这取决于要升级的资源数量。 对于小型部署（几十个受保护的实例），整个升级过程应会不到 20 分钟内完成。 对于大型部署，最长需要一小时。

**升级后是否可以回滚？**</br>
否。 成功升级资源后，就不支持回滚。

**是否可以验证订阅或资源，确定它们是否可升级？**</br>
是的。 升级过程的第一个步骤是验证资源是否可升级。 如果先决条件验证失败，会收到消息，其中包含无法完成升级的所有原因。

**应有哪些权限才能触发保管库升级？**</br>
若要执行保管库升级，必须在 Azure 经典门户中将你添加为订阅的协同管理员。 即使已在 Azure 门户中被列为所有者，仍需执行此操作。 在 Azure 经典门户中尝试为订阅添加协同管理员，确定你是否为订阅的协同管理员。 如果无法添加协同管理员，请联系订阅的服务管理员或协同管理员，他们可将你添加为协同管理员。

**是否可以升级基于 CSP 的备份保管库？**</br>
否。 目前无法升级基于 CSP 的备份保管库。 我们将在下一版本中添加对升级基于 CSP 的备份保管库的支持。

**升级后是否可以查看经典保管库？**</br>
否。 升级后无法查看或管理经典保管库。 只能使用新式 Azure 门户针对保管库执行所有管理操作。

升级失败，但保存需要升级的代理的计算机不再存在。对于这种情况，该怎么办？</br>
如果需要存储此计算机的备份以便长期保留，则无法升级保管库。 在将来版本中，我们将添加对升级此类保管库的支持。
如果不再需要存储此计算机的备份，请从保管库中注销此计算机，并重试升级。

升级后为何看不到资源的作业信息？</br>
备份监视（MARS 代理 和 IaaS）是将备份保管库升级到恢复服务保管库后获得一项新功能。 监视信息最长需要 12 小时才能与服务同步。

**如何报告问题？**</br>
如果保管库升级的任何环节失败，请记下错误中列出的 OperationId。 Microsoft 支持部门将主动解决此问题。 可以联系支持部门，或者向 rsvaultupgrade@service.microsoft.com 发送电子邮件并提供订阅 ID、保管库名称和 OperationId。 我们将尝试尽快解决此问题。 除非 Microsoft 明确指示，否则请不要重试操作。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：</br>
[备份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)</br>
[备份 Windows Server](backup-configure-vault.md)。

---
title: "将备份保管库升级到 Azure 备份的恢复服务保管库 | Microsoft Docs"
description: "将备份保管库升级到恢复服务保管库可获取新的功能，例如，资源管理器 VM 备份、增强的安全性、VMware VM 备份，以及 Windows Server 的系统状态备份"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: cfc2fde552b029412042474e31a1b28dd80b3021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>已升级到恢复服务保管库的备份保管库
本文提供恢复服务保管库的功能概述、有关将现有备份保管库升级到恢复服务保管库的常见问题，以及升级后的步骤。 恢复服务保管库是与用于容装备份数据的备份保管库相当的 Azure 资源管理器。 不管在本地还是在 Azure 中，数据通常都是虚拟机 (VM)、工作负荷、服务器或工作站的数据或配置信息的副本。

## <a name="what-is-a-recovery-services-vault"></a>什么是恢复服务保管库？
恢复服务保管库是 Azure 中的联机存储实体，用于保存备份副本、恢复点、备份策略之类的数据。 可以使用恢复服务保管库为各种 Azure 服务（例如 IaaS VM（Linux 或 Windows））和 Azure SQL 数据库存储备份数据。 恢复服务保管库支持 System Center DPM、Windows Server、Azure 备份服务器等。 使用恢复服务保管库可以方便地组织备份数据，并将管理开销降至最低。

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>比较恢复服务保管库和备份保管库
恢复服务保管库基于 Azure 的 Azure Resource Manager 模型，而备份保管库则基于 Azure Service Manager 模型。 将备份保管库升级到恢复服务保管库时，备份数据在升级过程中和升级后均会保持不变。 恢复服务保管库提供不适用于备份保管库的功能，例如：

- **有助于确保备份数据安全的增强功能**：使用恢复服务保管库时，Azure 备份提供用于保护云备份的安全功能。 这些安全功能确保可以保护备份并从云备份安全地恢复数据，即使生产服务器和备份服务器受损。 [了解详细信息](backup-azure-security-feature.md)

- **针对混合 IT 环境进行集中监视**：使用恢复服务保管库时，可以通过中心门户监视 [Azure IaaS VM](backup-azure-manage-vms.md) 和[本地资产](backup-azure-manage-windows-server.md#manage-backup-items)。 [了解详细信息](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **基于角色的访问控制 (RBAC)**：RBAC 在 Azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../active-directory/role-based-access-built-in-roles.md)，而 Azure 备份包含三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 恢复服务保管库兼容 RBAC，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

- **保护 Azure 虚拟机的所有配置**：恢复服务保管库保护基于 Resource Manager 的 VM，其中包括高级磁盘、托管磁盘和加密 VM。 将备份保管库升级到恢复服务保管库以后，即可将基于 Service Manager 的 VM 升级到基于 Resource Manager 的 VM。 在升级保管库的同时，可以保留基于 Service Manager 的 VM 恢复点，并为已升级（已启用 Resource Manager）的 VM 配置保护。 [了解详细信息](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **适用于 IaaS VM 的即时还原**：使用恢复服务保管库时，可以从 IaaS VM 还原文件和文件夹，不需还原整个 VM，从而缩短还原时间。 适用于 IaaS VM 的即时还原可供 Windows 和 Linux VM 使用。 [了解详细信息](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> 如果已使用版本低于 2.0.9083.0 的 MARS 代理将项注册到备份保管库，请[下载最新的 MARS 代理]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe)版本，以利用恢复服务保管库的所有功能。 
> 

## <a name="managing-your-recovery-services-vaults"></a>管理恢复服务保管库
以下屏幕显示了 Azure 门户中从备份保管库升级的新恢复服务保管库。 升级后的保管库将显示在名为“Default-RecoveryServices-ResourceGroup-geo”的默认资源组中。 示例：如果备份保管库位于美国西部，则它会放置在名为 Default-RecoveryServices-ResourceGroup-westus 的默认资源组中。
> [!NOTE]
> 对于 CPS 标准版客户，资源组在升级保管库后不会更改，与升级之前保持相同。

第一个屏幕显示保管库仪表板，其中显示了保管库的关键实体。
![从备份保管库升级的恢复服务保管库示例](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

第二个屏幕显示可助你开始使用恢复服务保管库的帮助链接。

![“快速启动”边栏选项卡中的帮助链接](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>升级后的步骤
恢复服务保管库支持在备份策略中指定时区信息。 成功升级保管库后，转到保管库设置菜单中的备份策略，并更新保管库中配置的每个策略的时区信息。 此屏幕已显示根据创建策略时所用的每个本地时区指定的备份计划时间。 

## <a name="enhanced-security"></a>增强的安全性
备份保管库升级到恢复服务保管库后，将自动启用该保管库的安全设置。 启用安全设置后，某些操作（如删除备份或更改密码）需要 [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md) PIN。 关于增强的安全性的详细信息，请参阅[用于保护混合备份的安全性功能](backup-azure-security-feature.md)一文。 启用增强的安全性后，数据将在恢复点信息从保管库中删除之后至多保留 14 天。 为此安全数据的存储对客户计费。 安全数据保留期适用于针对 Azure 备份代理、Azure 备份服务器和 System Center Data Protection Manager 的恢复点。 

## <a name="gather-data-on-your-vault"></a>收集保管库中的数据
升级到恢复服务保管库后，请配置 Azure 备份报表（适用于 IaaS VM 和 Microsoft Azure 恢复服务代理）并使用 Power BI 访问报表。 有关收集数据的其他信息，请参阅文章[配置 Azure 备份报表](backup-azure-configure-reports.md)。

## <a name="frequently-asked-questions"></a>常见问题

**升级计划是否影响正在进行的备份？**</br>
否。 升级期间和之后，正在进行的备份都会继续，而不会中断。

对于现有的工具而言，此升级有何意义？</br>
必须将现有的自动化或工具更新到资源管理器部署模型，确保其在升级后可继续运行。 请参阅 [Service Manager 部署模型](backup-client-automation-classic.md)和[资源管理器部署模型](backup-client-automation.md)的 PowerShell cmdlet 参考。

**升级后是否可以回滚？**</br>
不能。 成功升级资源后，就不支持回滚。

**升级后是否可以查看经典保管库？**</br>
否。 升级后无法查看或管理经典保管库。 只能使用新式 Azure 门户针对保管库执行所有管理操作。

**为何在升级后的保管库中看不到 MARS 代理保护的服务器？**</br>
需要安装最新的 MARS 代理才能查看保管库中受 MARS 代理保护的所有服务器。 可从[此处]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe)下载最新版本的代理。

**升级后看不到 MARS 代理保护的服务器的备份策略**</br>
保管库的备份策略可能已过期，因此无法同步到升级后的保管库。 请更新策略，确保能够继续看到已升级保管库中的策略。
若要更新策略，请转到 MARS 代理并更新已配置的备份策略。

**升级后为何无法更新备份策略？**</br>
如果在旧版备份代理上操作，并选择比允许的最小值更小的最短保留期，则会发生这种情况。 备份保管库升级到恢复服务保管库后，将自动启用该保管库的安全设置。 为了确保始终存在有效的可用恢复点数，需要根据安全功能维护某个最短保留期。 有关更多详细信息，请参阅[此文](backup-azure-security-feature.md)。
此外，需将 Azure 备份代理更新到最新版本，以利用 Azure 备份的最新功能。

**我已更新代理，但升级后即使过了好几天，也还是看不到有任何对象同步**</br>
请检查是否已将同一台计算机注册到多个保管库。 确保查看的保管库是 MARS 代理注册到的同一个保管库。 若要找出 MARS 代理所注册到的保管库，请打开 Windows 注册表，并检查 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config 下的 ServiceResourceName 项的值。MARS 代理所注册到的保管库就显示在该位置。 如果系统中未显示 ServiceResourceName 项，请联系我们并提供 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config 下的 ResourceId 和 MachineId 项的值，我们会帮助解决此问题。

升级后为何看不到资源的作业信息？</br>
备份监视（MARS 代理 和 IaaS）是将备份保管库升级到恢复服务保管库后获得一项新功能。 监视信息最长需要 12 小时才能与服务同步。

**如何报告问题？**</br>
如果保管库升级的任何环节失败，请记下错误中列出的 OperationId。 Microsoft 支持部门将主动解决此问题。 可以联系支持部门，或者向 rsvaultupgrade@service.microsoft.com 发送电子邮件并提供订阅 ID、保管库名称和 OperationId。 我们将尝试尽快解决此问题。 除非 Microsoft 明确指示，否则请不要重试操作。

## <a name="next-steps"></a>后续步骤
使用以下文章了解相关操作：</br>
[备份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)</br>
[备份 Windows Server](backup-configure-vault.md)

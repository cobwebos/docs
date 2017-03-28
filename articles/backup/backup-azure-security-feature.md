---
title: "使用 Azure 备份来保护混合备份的安全功能 | Microsoft Docs"
description: "了解如何在 Azure 备份中使用安全功能，使备份更加安全"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 5c49de40401235d38142efd60d22b0591752fe75
ms.openlocfilehash: f17802bf455b82f0b5239356c35024ecde7f1f35
ms.lasthandoff: 02/17/2017


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>使用 Azure 备份来保护混合备份的安全功能
越来越多的客户遇到恶意软件、勒索软件、入侵等安全问题。这些安全问题可导致数据丢失，每个安全漏洞的成本也与日俱增。 为了防止此类攻击，Azure 备份现提供可保护混合备份的安全功能。 本文介绍如何使用 Microsoft Azure 恢复服务代理和 Microsoft Azure 备份服务器来启用和利用这些功能。 这些功能建立在三大安全支柱的基础上：

1. **防护** - 执行关键操作（例如更改密码）时，会添加额外的身份验证层。 使用此验证，确保只有具有有效 Azure 凭据的用户才可执行此类操作。
2. **警报** - 执行关键操作（例如删除备份数据）时，会向订阅管理员发送电子邮件通知。 此电子邮件可确保用户及时收到有关此类操作的通知。
3. **恢复** - 删除的备份数据自删除之日起将另外保留 14 天。 这可确保能够在给定的时间段内恢复数据，因此即使受到攻击，也不会丢失数据。 此外，还保留了更多的最小恢复点，以防止数据损坏。

> [!NOTE]
> 只有使用以下项才应启用安全功能： <br/>
> * **MAB 代理** - 最小代理版本 2.0.9052。 启用这些功能后，应升级到此代理版本，以执行关键操作，例如更改密码、通过删除数据停止备份。 <br/>
> * **Azure 备份服务器** - Azure 备份服务器 Update 1 的 MAB 最低代理版本 2.0.9052。 <br/>
> * **DPM** - DPM 2012 R2 UR12 或 DPM 2016 UR2 的 MAB 最低代理版本 2.0.9052。 <br/>
> * **IaaS VM 备份** - 不对 IaaS VM 备份启用这些功能。 这些功能对于 IaaS VM 备份尚不可用，因此启用这些功能对 IaaS VM 备份没有任何影响。
> * 这些功能仅可用于恢复服务保管库。
> * 默认情况下，所有新创建的恢复服务保管库均具有这些功能。 对于现有的恢复服务保管库，用户需要使用下面部分中所述的步骤启用这些功能。
> * 启用后，将获得向保管库注册的所有 Azure 恢复服务代理 (MARS) 计算机、Azure 备份服务器和 DPM 服务器的安全功能。 <br/>
> * 启用此设置是一次性操作，启用这些功能后不能禁用它们。 <br/>
>
>

## <a name="enabling-security-features"></a>启用安全功能
创建恢复服务保管库的用户可利用所有安全功能。 对于现有恢复服务保管库，应使用以下步骤启用这些功能：

1. 使用 Azure 凭据登录到 Azure 门户
2. 在“中心”菜单键入恢复服务，导航到恢复服务列表。

    ![创建恢复服务保管库步骤 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    此时将显示恢复服务保管库列表。 从此列表中，选择一个保管库。

    此时将打开选定的保管库仪表板。
3. 从保管库下显示的项目列表中，单击“设置”下的“属性”。

    ![打开保管库属性](./media/backup-azure-security-feature/vault-list-properties.png)
4. 单击“安全设置”下的“更新”。

    ![打开安全设置](./media/backup-azure-security-feature/security-settings-update.png)

    更新链接将打开“安全设置”边栏选项卡，使你能够启用这些功能，并提供功能的摘要。
5. 从下拉框“是否已配置 Azure 多重身份验证?”选择一个值，确认是否已启用 [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md)。 如果已启用，则在登录到 Azure 门户时，系统会要求从另一设备（例如移动电话）进行身份验证。

   作为安全功能的一部分，在 Azure 备份中执行关键操作时，必须输入在 Azure 门户上可用的安全 PIN。 启用 Azure 多重身份验证会添加一个安全层，确保只有具有有效 Azure 凭据以及已从另一设备经身份验证的已授权用户可访问 Azure 门户并执行此类关键操作。
6. 如下图所示，使用切换按钮进行“启用”，单击顶部的“保存”按钮保存安全设置。 只有从“是否已配置 Azure 多重身份验证?”下拉框选择值后，才可选择“启用” 。

    ![启用安全设置](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recovering-deleted-backup-data"></a>恢复已删除的备份数据
如果通过删除备份数据操作执行停止备份，作为一种安全措施，Azure 备份会将已删除的备份数据另外再保留 14 天，不会立即删除数据。 若要在 14 天内还原此数据，请使用以下步骤：

对于 **Microsoft 恢复服务代理 (MARS)** 用户：

1. 如果发生备份的计算机仍可用，请在 MARS 中使用[将数据恢复到同一计算机](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)，从所有旧的恢复点恢复。
2. 如果上面提到的计算机不可用，则使用[恢复到备用计算机](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)，使用另一台 MARS 计算机获取此数据。

对于 **Azure 备份服务器**用户：

1. 如果发生备份的服务器仍可用，则重新保护已删除的数据源，并使用恢复数据功能从所有旧的恢复点恢复。
2. 如果上面提到的计算机不可用，则使用[从另一 Azure 备份服务器恢复数据](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server)，使用另一 Azure 备份服务器获取此数据。

对于 **Data Protection Manager (DPM)** 用户：

1. 如果发生备份的服务器仍可用，则重新保护已删除的数据源，并使用恢复数据功能从所有旧的恢复点恢复。
2. 如果上面提到的计算机不可用，则通过[添加外部 DPM](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) 来使用另一个 DPM 服务器获取此数据。

## <a name="preventing-attacks"></a>防止攻击
作为此功能的一部分，已添加检查，确保只有效用户才可执行各种操作。

### <a name="authentication-to-perform-critical-operations"></a>执行关键操作的身份验证
作为对关键操作添加额外身份验证层的一部分，在通过删除数据和更改密码操作执行停止保护时，系统将提示输入安全 PIN。

若要接收安全 PIN，请使用以下步骤：

1. 登录到 Azure 门户。
2. 导航到“恢复服务保管库”>“设置”>“属性”。
3. 单击安全 PIN 下的“生成”。 生成链接将打开一个边栏选项卡，其中包含要在 Azure 恢复服务代理 UI 中输入的安全 PIN。
    此 PIN 的有效时间仅为 5 分钟，并在 5 分钟后自动生成。

### <a name="maintaining-minimum-retention-range"></a>维持最少的保持期
为确保始终存在大量可用的有效恢复点，已添加以下检查：

1. 对于日保留期，应设置最少**七**天的保留期
2. 对于周保留期，应设置最少**四**周的保留期
3. 对于月保留期，应应设置最少**三**个月的保留期
4. 对于年保留期，应设置最少**一**年的保留期

## <a name="notifications-for-critical-operations"></a>关键操作的通知
执行关键操作时，将向订阅管理员发送包含该操作的详细信息的电子邮件通知。 如果想要配置其他电子邮件 ID 以接收电子邮件通知，可使用 Azure 门户进行配置。

此文章中提到的安全功能提供对针对性攻击的防御机制，阻止攻击者接触备份。 更重要的是，如果真的发生攻击，这些功能提供恢复数据的功能。

## <a name="next-steps"></a>后续步骤
* [Azure 恢复服务保管库入门](backup-azure-vms-first-look-arm.md)，用于启用这些功能
* [下载最新的 Azure 恢复服务代理](http://aka.ms/azurebackup_agent)，保护 Windows 计算机并防止备份数据受到攻击
* [下载最新的 Azure 备份服务器](https://aka.ms/latest_azurebackupserver)，保护工作负荷并防止备份数据受到攻击
* [下载适用于 System Center 2012 R2 Data Protection Manager 的 UR12](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) 或[下载适用于 System Center 2016 Data Protection Manager 的 UR2](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) 来保护工作负荷和备份数据免受攻击


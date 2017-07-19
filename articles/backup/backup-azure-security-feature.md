---
title: "有助于保护使用 Azure 备份的混合备份的安全功能 | Microsoft Docs"
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
ms.date: 06/08/2017
ms.author: pajosh
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 1400fe83bec85a7ab1b4c96fb38abdaf6c944845
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017


---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>有助于保护使用 Azure 备份的混合备份的安全功能
对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防止此类攻击，Azure 备份现提供可保护混合备份的安全功能。 本文介绍如何通过 Azure 恢复服务代理和 Azure 备份服务器来启用和使用这些功能。 这些功能包括：

- **防护**。 执行关键操作（例如更改密码）时，会添加额外的身份验证层。 使用此验证，确保只有具有有效 Azure 凭据的用户才可执行此类操作。
- **警报**。 执行关键操作（例如删除备份数据）时，会向订阅管理员发送电子邮件通知。 此电子邮件可确保用户快速收到有关此类操作的通知。
- **恢复**。 删除的备份数据自删除之日起将另外保留 14 天。 这可确保能够在给定的时间段内恢复数据，因此即使受到攻击，也不会丢失数据。 此外，还保留了更多的最小恢复点，以防止数据损坏。

> [!NOTE]
> 如果使用基础结构即服务 (IaaS) VM 备份，则不应启用安全功能。 这些功能对于 IaaS VM 备份尚不可用，因此启用这些功能没有任何影响。 只有使用以下项才应启用安全功能： <br/>
>  * **Azure 备份代理**。 最小代理版本 2.0.9052。 启用这些功能后，应升级到此代理版本，以执行关键操作。 <br/>
>  * **Azure 备份服务器**。 Azure 备份服务器 Update 1 的最低 Azure 备份代理版本为 2.0.9052。 <br/>
>  * **System Center Data Protection Manager**。 Data Protection Manager 2012 R2 UR12 或 Data Protection Manager 2016 UR2 的最低 Azure 备份代理版本为 2.0.9052。 <br/> 


> [!NOTE]
> 这些功能仅可用于恢复服务保管库。 默认情况下，所有新创建的恢复服务保管库均具有这些功能。 对于现有的恢复服务保管库，用户可以使用以下部分所述步骤启用这些功能。 这些功能在启用后，会应用到所有注册到保管库的恢复服务代理计算机、Azure 备份服务器实例以及 Data Protection Manager 服务器。 启用此设置是一次性操作，启用这些功能后不能禁用它们。
>

## <a name="enable-security-features"></a>启用安全功能
如果创建恢复服务保管库，则可使用所有安全功能。 如果使用现有的保管库，则可通过以下步骤启用安全功能：

1. 使用 Azure 凭据登录到 Azure 门户。
2. 选择“浏览”，然后键入“恢复服务”。

    ![Azure 门户“浏览”选项的屏幕截图](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    此时将显示恢复服务保管库列表。 从此列表中，选择一个保管库。 此时将打开选定的保管库仪表板。
3. 从保管库下显示的项目列表中，单击“设置”下的“属性”。

    ![恢复服务保管库选项的屏幕截图](./media/backup-azure-security-feature/vault-list-properties.png)
4. 单击“安全设置”下的“更新”。

    ![恢复服务保管库属性的屏幕截图](./media/backup-azure-security-feature/security-settings-update.png)

    更新链接将打开“安全设置”边栏选项卡，其中提供功能摘要，并允许你启用它们。
5. 从下拉列表“是否已配置 Azure 多重身份验证?”中选择一个值，确认是否已启用 [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md)。 如果已启用，则在登录到 Azure 门户时，系统会要求从另一设备（例如移动电话）进行身份验证。

   在备份中执行关键操作时，必须输入 Azure 门户中提供的安全 PIN。 启用多重身份验证相当于增加了一个安全层。 只有获得授权、具有有效 Azure 凭据且通过第二台设备进行身份验证的用户能够访问 Azure 门户。
6. 若要保存安全设置，请选择“启用”，然后单击“保存”。 只有从上一步的“是否已配置 Azure 多重身份验证?”列表中选择值后，才可选择“启用”。

    ![安全设置的屏幕截图](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>恢复已删除的备份数据
如果执行**停止备份并删除备份数据**操作，备份会将已删除的备份数据另外再保留 14 天，不会立即删除数据。 若要在 14 天的期限内还原该数据，请根据所用软件执行以下步骤：

对于 **Azure 恢复服务代理**用户：

1. 如果发生备份的计算机仍可用，请在 Azure 恢复服务中使用[将数据恢复到同一计算机](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)功能，从所有旧的恢复点恢复。
2. 如果该计算机不可用，则使用[恢复到备用计算机](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)功能，使用另一台 Azure 恢复服务计算机获取此数据。

对于 **Azure 备份服务器**用户：

1. 如果发生备份的服务器仍可用，则重新保护已删除的数据源，并使用**恢复数据**功能从所有旧的恢复点恢复。
2. 如果该服务器不可用，则使用[从另一 Azure 备份服务器恢复数据](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server)功能，通过另一 Azure 备份服务器实例获取此数据。

对于 **Data Protection Manager** 用户：

1. 如果发生备份的服务器仍可用，则重新保护已删除的数据源，并使用**恢复数据**功能从所有旧的恢复点恢复。
2. 如果该服务器不可用，则通过[添加外部 DPM](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) 来使用另一个 Data Protection Manager 服务器获取此数据。

## <a name="prevent-attacks"></a>防止攻击
已添加检查，确保只有效用户才可执行各种操作。 这些检查包括：添加额外的身份验证层，以及为恢复目的而保持一个最小的保留期时间范围。

### <a name="authentication-to-perform-critical-operations"></a>执行关键操作的身份验证
在为关键操作添加额外身份验证层的过程中，在执行**停止保护并删除数据**和**更改密码**操作时，系统会提示你输入安全 PIN。

若要接收此 PIN，请执行以下操作：

1. 登录到 Azure 门户。
2. 浏览到“恢复服务保管库” > “设置” > “属性”。
3. 单击“安全 PIN”下的“生成”。 此时会打开一个边栏选项卡，其中包含的 PIN 将输入到 Azure 恢复服务代理用户界面中。
    此 PIN 的有效时间仅为五分钟，并在五分钟后自动生成。

### <a name="maintain-a-minimum-retention-range"></a>维持最短的保持期
为确保始终存在大量可用的有效恢复点，已添加以下检查：

- 对于日保留期，应设置最少**七**天的保留期。
- 对于周保留期，应设置最少**四**周的保留期。
- 对于月保留期，应设置最少**三**个月的保留期。
- 对于年保留期，应设置最少**一**年的保留期。

## <a name="notifications-for-critical-operations"></a>关键操作的通知
通常情况下，执行关键操作时，将向订阅管理员发送包含该操作详细信息的电子邮件通知。 可以通过 Azure 门户为这些通知配置更多的电子邮件收件人。

此文章中提到的安全功能提供对针对性攻击的防御机制。 更重要的是，在发生攻击的情况下，这些功能为你提供恢复数据的能力。

## <a name="troubleshooting-errors"></a>排查错误
| 操作 | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 策略更改 |无法修改备份策略。 错误：由于内部服务错误 [0x29834]，当前操作失败。 请稍后重试操作。 如果该问题仍然存在，请联系 Microsoft 支持部门。 |原因：<br/>当启用安全设置、尝试缩短保留期范围至低于以上指定的最小值和使用不受支持的版本时，将出现此错误（本文第一条注释已指定所支持的版本）。 <br/>建议的操作<br/> 在这种情况下，应将保留期时段设置为高于指定保留期时间段的最小值（以日计为七天、以周记为四周、以月计为三个月或以年计为一年）以进行策略相关的更新。 （可选）首选更新备份代理、Azure 备份服务器和/或 DPM UR 来利用所有的安全性更新。 |
| 更改通行短语 |输入的安全 PIN 不正确。 (ID: 100130) 请提供正确的安全 PIN 来完成此操作。 |原因：<br/> 当执行关键操作（如更改通行短语）时输入无效或已过期的安全 PIN 将出现此错误。 <br/>建议的操作<br/> 若要完成该操作，必须输入有效的安全 PIN。 若要获取 PIN，登录到 Azure 门户并导航到“恢复服务保管库”>“设置”>“属性”>“生成安全 PIN”。 使用此 PIN 更改通行短语。 |
| 更改通行短语 |操作失败。 ID：120002 |原因：<br/>当启用安全设置、尝试更改通行短语和使用不受支持的版本时，将出现此错误（本文第一条注释已指定有效版本）。<br/>建议的操作<br/> 若要更改通行短语，必须首先更新备份代理至最低版本 minimum 2.0.9052、更新Azure 备份服务器至最低更新 1 和/或更新 DPM 至最低 DPM 2012 R2 UR12 或 DPM 2016 UR2 （下载链接如下），然后输入有效的安全 PIN。 若要获取 PIN，登录到 Azure 门户并导航到“恢复服务保管库”>“设置”>“属性”>“生成安全 PIN”。 使用此 PIN 更改通行短语。 |

## <a name="next-steps"></a>后续步骤
* [Azure 恢复服务保管库入门](backup-azure-vms-first-look-arm.md)，介绍如何启用这些功能。
* [下载最新的 Azure 恢复服务代理](http://aka.ms/azurebackup_agent)，保护 Windows 计算机并防止备份数据受到攻击。
* [下载最新的 Azure 备份服务器](https://aka.ms/latest_azurebackupserver)，保护工作负荷并防止备份数据受到攻击。
* [下载适用于 System Center 2012 R2 Data Protection Manager 的 UR12](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) 或[下载适用于 System Center 2016 Data Protection Manager 的 UR2](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager)，保护工作负荷和备份数据免受攻击。


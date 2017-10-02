---
title: "Azure AD Connect：从旧版升级 | Microsoft Docs"
description: "介绍升级到 Azure Active Directory Connect 最新版本的不同方法，包括就地升级和交叉迁移。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 52fd9375c71c42feaf87f4a0f4220e1cb3889e63
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect：从旧版升级到最新版本
本主题介绍可将 Azure Active Directory (Azure AD) Connect 安装升级到最新版本的不同方法。 建议使用最新版本的 Azure AD Connect。 进行重大配置更改时，也可以使用[交叉迁移](#swing-migration)部分所述的步骤。

如果要从 DirSync 升级，请参阅[从 Azure AD 同步工具 (DirSync) 升级](active-directory-aadconnect-dirsync-upgrade-get-started.md)。

可以采用几种不同的策略来升级 Azure AD Connect。

| 方法 | 说明 |
| --- | --- |
| [自动升级](active-directory-aadconnect-feature-automatic-upgrade.md) |对于使用快速安装的客户，这是最容易的方法。 |
| [就地升级](#in-place-upgrade) |如果只有一台服务器，可在该服务器上就地升级安装。 |
| [交叉迁移](#swing-migration) |如果有两台服务器，可将其中一台升级到最新版本或配置，然后在准备就绪时更改活动服务器。 |

有关权限信息，请参阅[升级所需权限](active-directory-aadconnect-accounts-permissions.md#upgrade)。

> [!NOTE]
> 启用新的 Azure AD Connect 服务器并开始将更改同步到 Azure AD 以后，不得通过回退来使用 DirSync 或 Azure AD Sync。不支持从 Azure AD Connect 降级到旧客户端（包括 DirSync 和 Azure AD Sync），那样可能会导致各种问题，例如数据在 Azure AD 中丢失。

## <a name="in-place-upgrade"></a>就地升级
就地升级适用于从 Azure AD Sync 或 Azure AD Connect 迁移。 它不适用于从 DirSync 迁移，也不适用于使用 Forefront Identity Manager (FIM) + Azure AD 连接器的解决方案。

如果只有一台服务器且对象数少于 100,000 个，则这是首选方法。 升级后，如果对现成的同步规则进行任何更改，则会发生完全导入和完全同步。 此方法可确保将新配置应用到系统中的所有现有对象。 此运行可能需要花费几小时的时间，具体取决于同步引擎作用域内的对象数。 正常增量同步计划程序（默认为每隔 30 分钟同步一次）会暂停，但密码同步会继续。 可以考虑在周末进行就地升级。 如果未对新版 Azure AD Connect 中的现成配置进行更改，则将改为启动一般的增量导入/同步。  
![就地升级](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

如果已更改现成的同步规则，这些规则会在系统升级完成之后重置为默认配置。 为了确保配置在每次升级之后得到保留，请务必按照[更改默认配置的最佳做法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)中所述的步骤来更改配置。

在就地升级过程中，可能会引入更改，要求在升级完成后执行特定同步活动（包括完全导入步骤和完全同步步骤）。 若要推迟这些活动，请参考[如何在升级后推迟完全同步](#how-to-defer-full-synchronization-after-upgrade)部分。

## <a name="swing-migration"></a>交叉迁移
如果部署复杂或者有多个对象，在活动的系统上进行就地升级可能不切合实际。 对于某些客户来说，此过程可能要花费几天时间，在此期间无法处理任何增量更改。 如果打算对配置进行重大更改，并且希望在将这些更改推送到云之前对其进行测试，则也可以使用此方法。

针对这些方案的建议方法是使用交叉迁移。 至少需要两台服务器，一台是活动服务器，另一台是过渡服务器。 活动服务器（在下图中以蓝色实线表示）负责处理活动的生产负载。 过渡服务器（以紫色虚线表示）已升级到最新版本或配置。 完全就绪以后，该服务器处于活动状态。 将目前安装了旧版本或配置的前一台活动服务器设为过渡服务器，然后进行升级。

两台服务器可以使用不同的版本。 例如，打算解除的活动服务器可以使用 Azure AD Sync，新的过渡服务器可以使用 Azure AD Connect。 如果使用交叉迁移来开发新配置，则建议在两台服务器上使用相同的版本。  
![暂存服务器](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> 对于这种方案，有些客户更愿意使用三到四台服务器进行交叉迁移。 升级过渡服务器后，将没有备份服务器用于[灾难恢复](active-directory-aadconnectsync-operations.md#disaster-recovery)。 如果使用三到四台服务器，就可以准备一组装有新版本的主服务器/待机服务器，确保始终都有用于接管的过渡服务器。

以下步骤也适用于从 Azure AD Sync 进行的迁移，或者从使用 FIM + Azure AD 连接器的解决方案进行的迁移。 这些步骤不适用于 DirSync，但是，可以在[升级 Azure Active Directory 同步 (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) 一文中找到适用于 DirSync 的相同交叉迁移（也称为并行部署）方法的步骤。

### <a name="use-a-swing-migration-to-upgrade"></a>使用交叉迁移来升级
1. 如果在两台服务器上使用 Azure AD Connect 并且只打算执行配置更改，请确保活动服务器和过渡服务器使用相同的版本。 这会有助于稍后比较差异。 如果要从 Azure AD Sync 升级，这些服务器将使用不同的版本。 如果要从旧版 Azure AD Connect 升级，建议使用相同的版本在两台服务器上开始升级，但不一定要这样做。
2. 如果创建了自定义配置，但过渡服务器没有该配置，请执行[将自定义配置从活动服务器移到过渡服务器](#move-custom-configuration-from-active-to-staging-server)部分的步骤。
3. 如果要从旧版 Azure AD Connect 升级，请将过渡服务器升级到最新版本。 如果要从 Azure AD Sync 迁移，请在过渡服务器上安装 Azure AD Connect。
4. 让同步引擎在过渡服务器上运行完全导入和完全同步。
5. 使用[验证服务器的配置](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)部分“验证”下面列出的步骤，验证新配置是否不会造成任何意外的更改。 如果出现异常，请按照相关步骤进行纠正，运行导入和同步，并对数据进行验证，直到一切正常。
6. 将过渡服务器切换为活动服务器。 这是[验证服务器的配置](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)中的最后一个步骤，即“切换活动服务器”。
7. 要升级 Azure AD Connect，请将现在处于过渡模式的服务器升级到最新版本。 按照与前面相同的步骤来升级数据和配置。 如果已从 Azure AD Sync 升级，现在可以关闭并解除旧服务器。

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>将自定义配置从活动服务器移到过渡服务器
如果对活动服务器做了配置更改，需确保将相同的更改应用到过渡服务器。 有关此移动的帮助，可以使用 [Azure AD Connect 配置文档](https://github.com/Microsoft/AADConnectConfigDocumenter)。

可以使用 PowerShell 移动所创建的自定义同步规则。 必须在两个系统上使用同一方式应用其他更改，不能迁移所做的更改。 [配置文档](https://github.com/Microsoft/AADConnectConfigDocumenter)可帮助你比较两个系统，以确保它们相同。 该工具还可帮助自动执行本部分中的步骤。

需在两个服务器上使用同一方式配置以下内容：

* 与相同林的连接
* 任何域和 OU 筛选
* 相同的可选功能，例如密码同步和密码写回

**移动自定义同步规则**  
若要移动自定义同步规则，请执行以下操作：

1. 在活动服务器上打开“同步规则编辑器”。
2. 选择自定义规则。 单击“导出”。 此时会打开一个记事本窗口。 使用 PS1 扩展名保存临时文件。 这样就可以将它转换为 PowerShell 脚本。 将此 PS1 文件复制到过渡服务器。  
   ![同步规则导出](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. 过渡服务器上的连接器 GUID 不同，因此必须更改。 要获取 GUID，请启动“同步规则编辑器”，选择表示同一个已连接系统的现成规则之一，并单击“导出”。 将 PS1 文件中的 GUID 替换为过渡服务器中的 GUID。
4. 在 PowerShell 命令提示符下运行 PS1 文件。 这会在过渡服务器上创建自定义同步规则。
5. 针对所有自定义规则重复此步骤。

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>如何在升级后推迟完全同步
在就地升级过程中，可能会引入更改，要求执行特定同步活动（包括完全导入步骤和完全同步步骤）。 例如，在受影响的连接器上，连接器架构更改要求执行“完全导入”步骤，现成同步规则更改要求执行“完全同步”步骤。 升级过程中，Azure AD Connect 确定必需执行哪些同步活动，并将它们记录为“替代”。 在以下同步周期中，同步计划程序将选取并执行这些替代。 成功执行替代后，会将其移除。

在某些情况下，可能不希望在升级后立即执行这些替代。 例如，具有大量已同步对象，并希望在工作时间结束后再执行同步步骤。 若要移除这些替代，请执行以下操作：

1. 在升级过程中，取消选中“在配置完成后启动同步流程”选项。 这将禁用同步计划程序，并防止在替代移除之前自动进入同步周期。

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. 升级完成后，运行以下 cmdlet，找出添加的替代：`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > 该替代特定于连接器。 以下示例中，已在本地 AD 连接器和 Azure AD 连接器中添加完全导入步骤和完全同步步骤。

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. 记下已添加的现有替代。
   
4. 若要删除任意连接器上的完全导入和完全同步替代，请运行以下 cmdlet：`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   若要删除所有连接器上的替代，请执行以下 PowerShell 脚本：

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. 若要恢复计划程序，请运行以下 cmdlet：`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > 请务必尽早执行必需的同步步骤。 可使用 Synchronization Service Manager 手动执行这些步骤，或使用 Set-ADSyncSchedulerConnectorOverride cmdlet 重新添加替代。

若要在任意连接器上添加完全导入和完全同步替代，请运行以下 cmdlet：`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。


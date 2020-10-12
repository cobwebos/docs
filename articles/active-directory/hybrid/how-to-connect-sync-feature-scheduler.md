---
title: Azure AD Connect 同步：计划程序 | Microsoft Docs
description: 本主题介绍 Azure AD Connect 同步中的内置计划程序功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084730"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect 同步：计划程序
本主题介绍 Azure AD Connect 同步（同步引擎）中的内置计划程序。

此功能是随内部版本 1.1.105.0（于 2016 年 2 月发布）一起推出的。

## <a name="overview"></a>概述
Azure AD Connect 同步会使用计划程序同步本地目录中发生的更改。 有两个计划程序进程，一个用于密码同步，另一个用于对象/属性同步和维护任务。 本主题涵盖后者。

在早期版本中，对象和属性的计划程序位于同步引擎外部。 它使用 Windows 任务计划程序或单独的 Windows 服务来触发同步进程。 计划程序在同步引擎的内置 1.1 版本中，并允许进行一些自定义。 新的默认同步频率为 30 分钟。

计划程序负责执行两项任务：

* **同步周期**。 用于导入、同步和导出更改的过程。
* **维护任务**。 续订用于密码重置和设备注册服务 (DRS) 的密钥和证书。 清除操作日志中的旧条目。

计划程序本身始终运行，但可以将它配置为仅运行其中一个任务或一个任务都不运行。 例如，如果需要运行自己的同步周期过程，则可以在计划程序中禁用此任务，但仍运行维护任务。

>[!IMPORTANT]
>默认情况下，每 30 分钟运行一个同步周期。 如果修改了同步周期，则需要确保至少每 7 天运行一个同步周期。 
>
>* 需要自上次增量同步后 7 天内进行增量同步。
>* 需要自上次完全同步完成后 7 天内进行增量同步（紧随完全同步）。
>
>如果未这样做，则可能会导致同步问题，而你需要运行完全同步才能解决该问题。 这也适用于处于暂存模式的服务器。

## <a name="scheduler-configuration"></a>计划程序配置
若要查看当前配置设置，请转到 PowerShell 并运行 `Get-ADSyncScheduler`。 它显示的内容如此图所示：

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

如果在运行此 cmdlet 时看到“此同步命令或 cmdlet 不可用”，则 PowerShell 模块未加载  。 如果在 PowerShell 限制级别高于默认设置的域控制器或服务器上运行 Azure AD Connect，则可能会发生这种问题。 如果看到此错误，则运行 `Import-Module ADSync` 可使该 cmdlet 可用。

* **AllowedSyncCycleInterval**。 Azure AD 允许的同步周期间的最短时间间隔。 不能比这一设置更频繁地同步，但仍会支持。
* **CurrentlyEffectiveSyncCycleInterval**。 当前生效的计划。 如果它不比 AllowedSyncInterval 更频繁，它具有与 CustomizedSyncInterval 相同的值（如果已设置）。 如果使用早于 1.1.281 的版本，且更改了 CustomizedSyncCycleInterval，该更改会在下一个同步周期后生效。 从版本 1.1.281 开始，更改会立即生效。
* **CustomizedSyncCycleInterval**。 如果希望计划程序以默认 30 分钟以外的任何其他频率运行，则可配置此设置。 在上图中，计划程序已改设为每隔一小时运行一次。 如果此项设置为低于 AllowedSyncInterval 的值，则使用后者。
* **NextSyncCyclePolicyType**。 Delta 或 Initial。 定义下次运行是只应处理增量更改，还是应执行完全导入和同步。后者还会重新处理任何新的或更改的规则。
* **NextSyncCycleStartTimeInUTC**。 计划程序启动下一个同步周期的时间。
* **PurgeRunHistoryInterval**。 操作日志应保留的时间。 可以在 Synchronization Service Manager 中查看这些日志。 默认设置是保留这些日志 7 天。
* **SyncCycleEnabled**。 指示计划程序是否正在运行导入、同步和导出过程作为其操作的一部分。
* **MaintenanceEnabled**。 显示是否启用了维护过程。 它会更新证书/密钥，并清除操作日志。
* **StagingModeEnabled**。 显示是否启用了[暂存模式](how-to-connect-sync-staging-server.md)。 如果启用此设置，它会取消运行导出，但仍运行导入和同步。
* **SchedulerSuspended**。 在升级过程中由 Connect 设置，以暂时阻止计划程序运行。

可以使用 `Set-ADSyncScheduler`更改上述一些设置。 可以修改以下参数：

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

在早期版本的 Azure AD Connect 中， **isStagingModeEnabled** 已在 Set-ADSyncScheduler 中公开。 **不支持** 设置此属性。 属性 **SchedulerSuspended** 只应通过 Connect 修改。 **不支持** 直接使用 PowerShell 设置此属性。

计划程序配置存储在 Azure AD 中。 如果具有暂存服务器，主服务器上的任何更改还将影响暂存服务器（IsStagingModeEnabled 除外）。

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
语法： `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - 天，HH - 小时，mm - 分钟，ss - 秒

示例： `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
将计划程序更改为每隔 3 小时运行一次。

示例： `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
将计划程序更改为每天运行一次。

### <a name="disable-the-scheduler"></a>禁用计划程序  
如果需要对配置进行更改，则要禁用计划程序。 例如，[配置筛选](how-to-connect-sync-configure-filtering.md)或[更改同步规则](how-to-connect-sync-change-the-configuration.md)时。

若要禁用计划程序，请运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。

![禁用计划程序](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

在完成更改后，请不要忘记通过 `Set-ADSyncScheduler -SyncCycleEnabled $true`再次启用计划程序。

## <a name="start-the-scheduler"></a>启动计划程序
默认情况下，计划程序每 30 分钟运行一次。 在某些情况下，可能想要在已计划的周期之间运行同步周期，或者需要运行不同的类型。

### <a name="delta-sync-cycle"></a>增量同步周期
增量同步周期包括以下步骤：


- 在所有连接器上增量导入
- 在所有连接器上增量同步
- 在所有连接器上导出

### <a name="full-sync-cycle"></a>完全同步周期
完全同步周期包括以下步骤：

- 在所有连接器上完全导入
- 在所有连接器上完全同步
- 在所有连接器上导出

有时可能有必须立即同步的紧急更改，这就是为什么需要手动运行周期的原因。 

如果需要手动运行同步周期，则从 PowerShell 运行 `Start-ADSyncSyncCycle -PolicyType Delta`。

若要启动完全同步周期，请在 PowerShell 提示符下运行 `Start-ADSyncSyncCycle -PolicyType Initial` 。   

运行完全同步周期可能非常耗时，请阅读下一部分以了解如何优化此过程。

### <a name="sync-steps-required-for-different-configuration-changes"></a>不同配置更改所需的同步步骤
不同的配置更改需要不同的同步步骤，以确保更改正确应用于所有对象。

- 添加了更多要从源目录导入的对象或属性（通过添加/修改同步规则）
    - 对于该源目录，连接器需要完全导入
- 更改了同步规则
    - 对于更改的同步规则，连接器需要完全同步
- 更改了[筛选设置](how-to-connect-sync-configure-filtering.md)，因此应包含不同的对象数
    - 对于每个 AD 连接器，连接器都需要完全导入，除非你使用基于属性的筛选（基于已经导入到同步引擎中的属性）

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>自定义同步周期会运行增量同步和完全同步步骤的正确组合
若要避免运行完全同步周期，可以使用以下 cmdlet 标记特定连接器以运行完整步骤。

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

示例：如果你更改了不需要导入任何新属性的连接器“AD Forest A”的同步规则，则可以运行以下 cmdlet 来运行增量同步周期，该周期也会对该连接器执行完全同步步骤。

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

示例：如果你更改了连接器“AD Forest A”的同步规则，于是它们现在需要导入新属性，则可运行以下 cmdlet 以运行增量同步周期，该周期也会对该连接器执行完全导入、完全同步步骤。

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>停止计划程序
如果计划程序当前正在运行同步周期，可能需要将其停止。 例如，如果启动安装向导并收到以下错误：

![屏幕截图显示无法更改配置错误消息。](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

正在运行同步周期时，不能进行配置更改。 可以等到计划程序已完成该过程，但也可以将其停止，以便可以立即进行更改。 停止当前周期没有任何害处，挂起的更改会在下次运行时处理。

1. 先要使用 PowerShell cmdlet `Stop-ADSyncSyncCycle`指示计划程序停止其当前周期。
2. 如果使用 1.1.281 之前的版本，停止计划程序并不会使当前连接器停止执行其当前任务。 若要强制停止连接器，请执行以下操作：

   ![屏幕截图显示选定连接器 Synchronization Service Manager，并选中 "停止" 操作，突出显示正在运行的连接器。](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * 从“开始”菜单启动“同步服务”。  转到“连接器”，突出显示状态为“正在运行”的连接器，然后从“操作”中选择“停止”。   

计划程序仍处于活动状态，并在下次有机会时重新启动。

## <a name="custom-scheduler"></a>自定义计划程序
本节中所述的 cmdlet 仅在内部版本 [1.1.130.0](reference-connect-version-history.md) 及更高版本中提供。

如果内置的计划程序不符合要求，则可以使用 PowerShell 计划连接器。

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
可以用这种方式为连接器启动配置文件：

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

用于[连接器名称](how-to-connect-sync-service-manager-ui-connectors.md)和[运行配置文件名称](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles)的名称可以在 [Synchronization Service Manager UI](how-to-connect-sync-service-manager-ui.md) 中找到。

![调用运行配置文件](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` cmdlet 是同步的，即在连接器完成操作（无论成功还是出错）之前，它不会返回控制。

计划连接器时，建议按以下顺序进行计划：

1. 从本地目录（如 Active Directory）中（完全/增量）导入
2. 从 Azure AD 中（完全/增量）导入
3. 从本地目录（如 Active Directory）（完全/增量）同步
4. 从 Azure AD（完全/增量）同步
5. 导出到 Azure AD
6. 导出到本地目录，如 Active Directory

此顺序即内置计划程序运行连接器的原理。

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
还可以监视同步引擎以了解它是忙还是空闲。 如果同步引擎处于空闲状态且未运行连接器，则此 cmdlet 返回一个空结果。 如果连接器正在运行，则返回连接器的名称。

```
Get-ADSyncConnectorRunStatus
```

![连接器运行状态](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
在上图中，第一行来自同步引擎处于空闲的状态。 第二行来自 Azure AD 连接器正在运行时。

## <a name="scheduler-and-installation-wizard"></a>计划程序和安装向导
如果启动安装向导，计划程序会暂时暂停。 此行为是因为它认为用户将进行配置更改，如果同步引擎正处于活动运行状态，则不能应用这些设置。 出于此原因，不要让安装向导处于打开状态，因为这会使同步引擎停止执行任何同步操作。

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](how-to-connect-sync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。

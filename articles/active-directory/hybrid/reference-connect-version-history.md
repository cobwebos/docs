---
title: Azure AD Connect：版本发布历史记录 | Microsoft Docs
description: 本文列出了 Azure AD Connect 和 Azure AD Sync 的所有版本。
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1570f4a8c4fb23b885c1de68c2c8d2440c4f6aae
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018435"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect：版本发行历史记录
Azure Active Directory (Azure AD) 团队会定期更新 Azure AD Sync 的新特性和功能。 并非所有的新增内容都适用于所有受众。

本文旨在帮助你跟踪已发布的版本，并了解最新版本中的具体变化。



下表列出了相关主题：

主题 |  详细信息
--------- | --------- |
从 Azure AD Connect 升级的步骤 | [从旧版升级到最新版](how-to-upgrade-previous-version.md) Azure AD Connect 的不同方法。
所需的权限 | 有关应用更新时所需的权限，请参阅[帐户和权限](reference-connect-accounts-permissions.md#upgrade)。
下载| [下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)。

>[!NOTE]
>发布新版 Azure AD Connect 的过程需要采取多个质量控制的步骤来确保服务操作的功能，在执行此过程时，新版本的版本号和发布状态会进行更新，以反映最新状态。
在执行此过程时，版本的版本号将以“X”的形式在次要版本号位置显示，如“1.3.X.0”，这个例子表明本文档中的发行说明适用于以“1.3.”开头的所有版本。 完成发布过程后，发行版本号将更新为最近发布的版本，发布状态将更新为“已发布，用于下载和自动升级”。
并非所有版本的 Azure AD Connect 都可用于自动升级。 版本状态将指示版本是否可用于自动升级或仅供下载。 如果在 Azure AD Connect 服务器上启用了自动升级，那么该服务器将自动升级到针对自动升级发布的最新版 Azure AD Connect。 请注意，并非所有 Azure AD Connect 配置都有资格进行自动升级。 请点击此链接阅读有关[自动升级](how-to-connect-install-automatic-upgrade.md)的详细信息

>[!IMPORTANT]
> 从 2020 年 11 月 1 日开始，我们将开始实现一个弃用过程，即弃用发布时间超过 18 个月的 Azure AD Connect 版本。 届时，我们将弃用已发布的所有版本为 1.3.20.0（发布日期为 2019/4/24）及更早版本的 Azure AD Connect，从而开启这一过程，而在每次发布新版本时，我们将继续对是否弃用旧版 Azure AD Connect 进行评估。
>
> 需要确保运行最新版本的 Azure AD Connect，才能获得最佳支持体验。 
>
>如果运行已弃用的 Azure AD Connect 版本，则可能无法获得最新的安全修补程序、性能改进、故障排除和诊断工具以及服务增强功能，同时，如果需要支持，我们可能无法为你提供组织所需的服务级别。
>
>如果已启用支持同步的 Azure AD Connect，则在运行某个较旧版本时，你很快就会自动开始接收运行状况通知，警告你该版本即将弃用。
>
>请参阅[本文](./how-to-upgrade-previous-version.md)，详细了解如何将 Azure AD Connect 升级到最新版本。
>
>有关弃用版本的版本历史记录信息，请参阅 [Azure AD Connect 版本发行历史记录存档](reference-connect-version-history-archive.md)

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>版本状态
07/29/2020：已发布以供下载

### <a name="functional-changes"></a>功能性更改
这是一个 bug 修复版本。 此版本中没有功能更改。

### <a name="fixed-issues"></a>已修复的问题

- 修复了在 "Active Directory" 中，管理员无法启用 "无缝单一登录" 的问题。
- 修复了在 V2 API 增量导入过程中导致暂存错误的问题，该问题是通过运行状况门户修复的冲突对象。
- 修复了导入/导出配置中禁用自定义规则导入为启用的问题。

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>版本状态
07/10/2020：已发布以供下载

### <a name="functional-changes"></a>功能性更改
此版本包含用于将现有 Azure AD Connect 服务器的配置导出到中的功能的公共预览版。JSON 文件，在安装新的 Azure AD Connect 服务器以创建原始服务器的副本时，可以使用该文件。

可在[此文](./how-to-connect-import-export-config.md)中找到此新功能的详细说明

### <a name="fixed-issues"></a>已修复的问题
- 修复了在升级过程中会出现有关本地化版本上的本地数据库大小的错误警告。
- 修复了帐户名称/域名交换的应用事件中出现错误的 bug。
- 修复了 Azure AD Connect 将无法在 DC 上安装的错误，并提供错误 "找不到成员"。


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>版本状态
2020/05/07：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
此修补程序版本修复了以下问题：仅选择孙级容器时，便会从向导 UI 中错误地选择未选的域。


>[!NOTE]
>此版本包含新的 Azure AD Connect 同步 V2 终结点 API。  这个新的 V2 终结点目前以公共预览版提供。  使用新的 V2 终结点 API 需要此版本或更高版本。  但是，单是安装此版本不会启用 V2 终结点。 除非启用 V2 终结点，否则将继续使用 V1 终结点。  需要按照 [Azure AD Connect 同步 V2 终结点 API（公共预览版）](how-to-connect-sync-endpoint-api-v2.md)下的步骤进行操作，才能为启用该终结点并选择加入公共预览版。  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>版本状态
2020/04/23：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
此修补程序版本修复了版本 1.5.20.0 中引入的一个问题：具有 MFA 的租户管理员无法启用 DSSO。

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>版本状态
2020/04/20：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
在已克隆 In from AD - Group Join 规则但尚未克隆 In from AD - Group Common 规则的情况下，则此修补程序版本会修复版本 1.5.20.0 中的一个问题 。

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>版本状态
2020/04/09：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
- 在已启用组筛选功能并使用 mS-DS-ConsistencyGuid 作为源定位点的情况下，此修补程序版本会修复版本 1.5.18.0 中的一个问题。
- 修复了 ADSyncConfig PowerShell 模块中的一个问题，其中调用所有 Set-ADSync* Permissions cmdlet 中使用的 DSACLS 命令将导致以下某个错误：
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> 如果已克隆 In from AD - Group Join 同步规则但未克隆 In from AD - Group Common 同步规则，并计划升级，请在升级过程中完成以下步骤 ：
> 1. 在升级过程中，取消选中“在配置完成后启动同步流程”选项。
> 2. 编辑克隆的联接同步规则并添加以下两个转换：
>     - 将直接流 `objectGUID` 设置为 `sourceAnchorBinary`。
>     - 将表达式流 `ConvertToBase64([objectGUID])` 设置为 `sourceAnchor`。     
> 3. 使用 `Set-ADSyncScheduler -SyncCycleEnabled $true` 启用计划程序。



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>版本状态
2020/04/02：已发布，供下载

### <a name="functional-changes-adsyncautoupgrade"></a>功能性更改 ADSyncAutoUpgrade 

- 添加了对组对象的 mS-DS-ConsistencyGuid 功能的支持。 这样，你便可以在林之间移动组，或在 AD 组 objectID 已更改的情况下将 AD 中的组重新连接到的 Azure AD，例如在灾难后重新生成 AD 服务器时。 有关详细信息，请参阅[在林之间移动组](how-to-connect-migrate-groups.md)。
- 所有同步组上会自动设置 mS-DS-ConsistencyGuid 属性，并且无需执行任何操作即可启用此功能。 
- 删除了 Get-ADSyncRunProfile，因为不会再使用它。 
- 更改了尝试使用 AD DS 连接器帐户的企业管理员帐户或域管理员帐户时显示的警告，以提供更多上下文。 
- 添加了新的 cmdlet，以从已删除旧 CSDelete.exe 工具的连接器空间中删除对象，并将该工具替换为新的 Remove-ADSyncCSObject cmdlet。 Remove-ADSyncCSObject cmdlet 将 CsObject 作为输入。 可使用 Get-ADSyncCSObject cmdlet 检索此对象。

>[!NOTE]
>旧的 CSDelete.exe 工具已删除，并已替换为新的 Remove-ADSyncCSObject cmdlet 

### <a name="fixed-issues"></a>修复的问题

- 修复了以下问题：在禁用 Azure AD Connect 向导后重新运行该功能时，组写回林/OU 选择器中存在 bug。 
- 引入了一个新的错误页，如果缺少所需的 DCOM 注册表值，该页会显示新的帮助链接。 信息还会写入日志文件。 
- 修复了与创建 Azure Active Directory 同步帐户有关的问题：启用目录扩展或 PHS 可能会失败，因为在尝试使用之前未在所有服务副本之间传播该帐户。 
- 修复了同步错误压缩实用程序中的 bug：未正确处理代理项字符。 
- 修复了自动升级中的 bug：将服务器置于计划程序已挂起状态。 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>版本状态
2019/12/9：已发布，供下载。 无法通过自动升级获得。
### <a name="new-features-and-improvements"></a>新增功能和改进
- 为 Azure AD 域服务更新了密码哈希同步，以正确解释 Kerberos 哈希中的填充。  这将在从 Azue AD 到 Azure AD 域服务的密码同步期间提高性能。
- 添加了对身份验证代理和服务总线之间的可靠会话的支持。
- 此版本会对身份验证代理与云服务之间的通信强制实施 TLS 1.2。
- 为身份验证代理和云服务之间的 WebSocket 连接添加了 DNS 缓存。
- 新增了从云中针对特定代理测试代理连接性的功能。

### <a name="fixed-issues"></a>修复的问题
- 版本 1.4.18.0 有一个 bug：在运行 ps 时，用于 DSSO 的 PowerShell cmdlet 使用的是登录窗口凭据，而不是提供的管理员凭据。 因此，无法通过 Azure AD Connect 用户界面在多个林中启用 DSSO。 
- 已通过 Azure AD Connect 用户界面在所有林中同时启用了 DSSO

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>版本状态
2019/11/08：已发布，供下载。 无法通过自动升级获得。

>[!IMPORTANT]
>由于该版本 Azure AD Connect 的内部架构发生了更改，因此，如果使用 MSOnline PowerShell 管理 AD FS 信任关系配置设置，则必须将 MSOnline PowerShell 模块更新为版本 1.1.183.57 或更高版本

### <a name="fixed-issues"></a>修复的问题

此版本修复了现有已建立混合 Azure AD 联接的设备方面的问题。 此版本包含新的设备同步规则，可修正此问题。
请注意，此规则更改可能会从 Azure AD 删除过时的设备。 不必担心，因为在条件访问授权期间 Azure AD 不会使用这些设备对象。 对于某些客户，通过此规则更改删除的设备数量可能会超出删除阈值。 如果看到 Azure AD 中设备对象的删除数量超过了导出删除阈值，建议允许删除。 [如何在删除数量超出删除阈值时允许其流动](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>版本状态
2019/9/28：已发布，用于自动升级以选择租户。 不可供下载。

此版本修复了一个 bug：某些服务器从先前版本自动升级到 1.4.18.0，并遇到了自助式密码重置 (SSPR) 和密码写回问题。

### <a name="fixed-issues"></a>修复的问题

在某些情况下，自动升级到版本 1.4.18.0 的服务器不会在升级完成后重新启用自助式密码重置和密码写回。 此自动升级版本修复了该问题，并重新启用了自助式密码重置和密码写回。

我们修复了同步错误压缩实用程序中的 bug：未正确处理代理项字符。

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>我们正在调查一个事件：某些客户在升级到这一版本的 Azure AD Connect 后，遇到了现有已建立混合 Azure AD 联接的设备方面的问题。 我们建议已部署混合 Azure AD 联接的客户推迟升级为这一版本，一直到完全了解这些问题的根本原因并缓解这些问题为止。 我们将尽快提供详细信息。

>[!IMPORTANT]
>对于这一版本的 Azure AD Connect，某些客户可能会看到其部分或所有 Windows 设备从 Azure AD 中消失。 不必担心，因为在条件访问授权期间 Azure AD 不会使用这些设备标识。 有关详细信息，请参阅[了解 Azure AD Connect 1.4.xx.x 设备消失](reference-connect-device-disappearance.md)


### <a name="release-status"></a>版本状态
2019/9/25：已发布，仅用于自动升级。

### <a name="new-features-and-improvements"></a>新增功能和改进
- 新疑难解答工具有助于对“用户不同步”、“组不同步”或“组成员不同步”的情况进行故障排除。
- Azure AD Connect 疑难解答脚本中添加对国家/地区云的支持 
- 客户应接到关于以下信息的通知：MIIS_Service 已弃用的 WMI 终结点现已删除。 现在，任何 WMI 操作都应通过 PS cmdlet 完成。
- 通过重置对 AZUREADSSOACC 对象的约束委派提高了安全性
- 添加/编辑同步规则时，如果规则中使用的任何属性都在连接器架构中，但未添加到连接器，那么这些属性都会自动添加到连接器。 对于规则影响的对象类型也是如此。 如果在连接器中添加了任何内容，则会在下一个同步周期中将连接器标记为完全导入。
- 新的 Azure AD Connect 部署中不再支持使用企业管理员或域管理员作为连接器帐户。 使用企业管理员或域管理员作为连接器帐户的当前 Azure AD Connect 部署不受此版本的影响。
- 在同步管理器中，创建/编辑/删除规则时会运行完全同步。 进行任何规则更改时，都将显示一个弹出窗口，通知用户是否要运行完全导入或完全同步。
- 向“连接器”>“属性”>“连接”页中添加了密码错误的缓解步骤添加
- 在连接器属性页上为同步服务管理器添加了弃用警告。 此警告通知用户应通过 Azure AD Connect 向导进行更改。
- 为用户密码策略相关问题新增了错误消息。
- 防止按域和 OU 筛选器对组筛选进行错误配置。 当所输入组的域/OU 已筛选出来时，组筛选将显示错误，并且会在问题解决前阻止用户继续执行操作。
- 用户无法再在 Synchronization Service Manager UI 中为 Active Directory 域服务或 Windows Azure Active Directory 创建连接器。
- 在 Synchronization Service Manager 中修复了自定义 UI 控件的辅助功能。
- 为 Azure AD Connect 中的所有登录方法启用了六项联合身份验证管理任务。  （以前，只有“更新 AD FS TLS/SSL 证书”任务可用于所有登录。）
- 在将登录方法从联合身份验证更改为 PHS 或 PTA 时添加了警告，提醒所有 Azure AD 域和用户都将转换为托管身份验证。
- 从“重置 Azure AD 和 AD FS 信任”任务中删除了令牌签名证书，并添加了单独的子任务来更新这些证书。
- 添加了名为“管理证书”的新联合身份验证管理任务，其中包含用于更新 AD FS 场的 TLS 或令牌签名证书的子任务。
- 添加了名为“指定主服务器”的新联合管理子任务，该任务允许管理员为 AD FS 场指定新的主服务器。
- 添加了名为“管理服务器”的新联合身份验证管理任务，其中包含部署 AD FS 服务器、部署 Web 应用程序代理服务器和指定主服务器的子任务。
- 添加了名为“查看联合身份验证配置”的新联合身份验证管理任务，其中显示了当前的 AD FS 设置。  （由于这项添加内容，AD FS 设置已从“查看解决方案”页中删除。）

### <a name="fixed-issues"></a>修复的问题
- 解决了以下场景中出现的同步错误问题：接管其相应联系人对象的用户对象存在自引用（例如，用户是他们自己的管理员）。
- “帮助”弹出窗口现在显示在键盘焦点上。
- 对于自动升级，如果在 6 小时后有任何存在冲突的应用运行，请将其终止，然后继续升级。
- 选择目录扩展时，将客户可为每个对象选择的属性数限制为 100。 这将防止在导出过程中发生错误，因为 Azure 每个对象最多具有 100 个扩展属性。
- 修复了一个 bug，使 AD 连接性脚本更加可靠
- 修复了一个 bug，使用现有的 Named Pipes WCF 服务将 Azure AD Connect 安装到计算机上。
- 改进了有关组策略的诊断和疑难解答，这些策略不允许在最初安装时启动 ADSync 服务。
- 修复了以下 bug：Windows 计算机的显示名称写入错误。
- 修复了以下 bug：Windows 计算机的操作系统类型写入错误。
- 修复了以下 bug：非 Windows 10 计算机意外进行同步。 请注意，此更改的影响是，现在将删除之前同步的非 Windows 10 计算机。 这不会影响任何功能，因为 Windows 计算机的同步仅用于混合 Azure AD 域加入，这仅适用于 Windows 10 设备。
- 向 ADSync PowerShell 模块添加了多个新的（内部）cmdlet。


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>将 Azure AD Connect 从早期版本升级到1.3.21.0 的已知问题，其中 Microsoft 365 门户不会反映更新的版本，即使 Azure AD Connect 升级成功。
>
> 若要解决此问题，需要导入 AdSync 模块，然后在 Azure AD Connect 服务器上运行 `Set-ADSyncDirSyncConfiguration` PowerShell cmdlet。  可以使用以下步骤：
>
>1. 在管理员模式下打开 PowerShell。
>2. 运行 `Import-Module "ADSync"`。
>3. 运行 `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`。
 
### <a name="release-status"></a>版本状态 

2019/05/14：已发布，供下载

### <a name="fixed-issues"></a>修复的问题 

- 修复了 Microsoft Azure Active Directory Connect 版本 1.3.20.0 中存在的特权提升漏洞。  在某些情况下，此漏洞可能允许攻击者利用特权帐户来执行两个 PowerShell cmdlet，并执行特权操作。  此安全更新程序通过禁用这些 cmdlet 解决了此问题。 有关详细信息，请参阅[安全更新](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)。


## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
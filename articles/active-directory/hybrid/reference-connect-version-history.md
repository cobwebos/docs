---
title: Azure AD Connect：版本发布历史记录 | Microsoft Docs
description: 本文列出 Azure AD Connect 和 Azure AD Sync 的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: bc3c572aeb72328bc4708d27052756623ccd7701
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200976"
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
>发布新版 Azure AD Connect 的过程要求采取多种质量控制措施来确保服务的功能正常运行，遵循此过程时，我们会更新新发行版的版本号以及发布状态，以反映最近的状态。
遵循此过程时，发行版的版本号将以“X”形式显示在次要版本号位置，例如“1.3.X.0”- 这表示此文档中的发行说明适用于以“1.3”开头的所有版本。 完成发布过程后，我们会立即将发行版本号更新为最近发布的版本，并将发布状态更新为“已发布供下载和自动升级”。
并非所有版本的 Azure AD Connect 都可用于自动升级。 版本状态将指示版本是否可用于自动升级或仅供下载。 如果在 Azure AD Connect 服务器上启用了自动升级，那么该服务器将自动升级到针对自动升级发布的最新版 Azure AD Connect。 请注意，并非所有 Azure AD Connect 配置都有资格进行自动升级。 请点击此链接阅读有关[自动升级](how-to-connect-install-automatic-upgrade.md)的详细信息

>[!IMPORTANT]
> 从 2020 年 11 月 1 日开始，我们将开始实施一个弃用过程，即弃用发布时间已超过 18 个月的 Azure AD Connect 版本。 到那时，我们将弃用所有已发布的版本为 1.3.20.0（发布于 2019/4/24）及更旧版本的 Azure AD Connect，并会在每次发布新版本时，对是否弃用旧版 Azure AD Connect 进行评估。
>
> 需要确保运行最新版本的 Azure AD Connect，才能获得最佳的支持体验。 
>
>如果你运行已弃用的 Azure AD Connect 版本，则可能没有最新的安全修补程序、性能改进、故障排除和诊断工具以及服务增强功能。在你需要支持的情况下，我们可能无法为你提供你的组织需要的服务级别。
>
>如果已启用用于同步的 Azure AD Connect，则在运行某个较旧的版本时，你很快就会自动收到运行状况通知，警告你该版本即将弃用。
>
>请参阅[此文](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)，详细了解如何将 Azure AD Connect 升级到最新版本。

## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>版本状态
05/07/2020：已发布以供下载

### <a name="fixed-issues"></a>修复的问题
- 修复了从向导 UI 中未正确选择未选择的域的问题。
- 修复了 ADSyncConfig PowerShell 模块中的一个问题，其中在所有 ADSync * 权限 cmdlet 中使用的 DSACLS 命令都将导致以下错误之一：
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>版本状态
04/23/2020：已发布以供下载

### <a name="fixed-issues"></a>修复的问题
此修补程序版本修复了生成1.5.20.0 中引入的一个问题，其中，具有 MFA 的租户管理员无法启用 DSSO。

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>版本状态
04/20/2020：已发布以供下载

### <a name="fixed-issues"></a>修复的问题
如果已**从 Ad 组联接**规则克隆入，且尚未**从 ad 组公共**规则克隆入，则此修补程序版本会修复生成1.5.20.0 中的问题。

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>版本状态
04/09/2020：已发布以供下载

### <a name="fixed-issues"></a>修复的问题
如果已启用组筛选功能并使用 Msds-consistencyguid 作为源锚点，则此修补程序版本会修复生成1.5.18.0 的问题。

> [!IMPORTANT]
> 如果已**从 Ad 组加入**同步规则克隆到中，并且尚未**从 ad 组公共**同步规则克隆到，并且计划升级，请在升级过程中完成以下步骤：
> 1. 在升级过程中，取消选中 "**配置完成后启动同步过程**" 选项。
> 2. 编辑克隆的联接同步规则并添加以下两个转换：
>     - 将 "直接流" 设置 `objectGUID` 为 `sourceAnchorBinary` 。
>     - 将表达式流设置 `ConvertToBase64([objectGUID])` 为 `sourceAnchor` 。     
> 3. 使用启用计划程序 `Set-ADSyncScheduler -SyncCycleEnabled $true` 。



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>版本状态
04/02/2020：已发布以供下载

### <a name="functional-changes-adsyncautoupgrade"></a>功能更改 Set-adsyncautoupgrade 

- 添加了对组对象的 Msds-consistencyguid 功能的支持。 这样，你便可以在 ad 组 objectID 发生更改的情况下，将组在林之间移动或重新连接组 Azure AD，例如，在灾难之后重新生成 AD 服务器。 有关详细信息，请参阅[在林之间移动组](how-to-connect-migrate-groups.md)。
- 将在所有同步组上自动设置 Msds-consistencyguid 属性，并且无需执行任何操作即可启用此功能。 
- 已删除 ADSyncRunProfile，因为它已不再使用。 
- 更改了在尝试使用企业管理员帐户或域管理员帐户进行 AD DS 连接器帐户以提供更多上下文时显示的警告。 
- 添加了新的 cmdlet，以从连接器空间中删除对象旧的 CSDelete 工具，并将其替换为新的 ADSyncCSObject cmdlet。 ADSyncCSObject cmdlet 使用 CsObject 作为输入。 可使用 ADSyncCSObject cmdlet 检索此对象。

>[!NOTE]
>旧的 CSDelete 工具已删除并已替换为新的 ADSyncCSObject cmdlet 

### <a name="fixed-issues"></a>修复的问题

- 修复了在禁用此功能后重新运行 Azure AD Connect 向导时，组写回林/OU 选择器中的错误。 
- 引入了一个新的错误页，当所需的 DCOM 注册表值缺少新的帮助链接时，将显示此页。 还会将信息写入日志文件。 
- 解决了创建 Azure Active Directory 同步帐户时，启用目录扩展或 PHS 可能会失败的问题，因为在尝试使用之前，该帐户尚未传播到所有服务副本。 
- 修复了不能正确处理代理项字符的同步错误压缩实用工具中的 bug。 
- 修复了自动升级中的一个 bug，该 bug 将服务器置于计划程序挂起状态。 
- 修复了 "域/OU 筛选" 页中的一个 bug，该 bug 只需部分展开域树即可删除域的运行配置文件，而无需进行任何更改。

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>版本状态
12/9/2019：发布以供下载。 不通过自动升级提供。
### <a name="new-features-and-improvements"></a>新增功能和改进
- 考虑到 Azure AD Connect 哈希中的填充，我们更新了用于 Azure AD 域服务的密码哈希同步。  这样就可以在从 AAD 到 Azure AD 域服务的密码同步期间提高性能。
- 我们添加了对身份验证代理和服务总线之间的可靠会话的支持。
- 此版本强制执行 TLS 1.2，适用于身份验证代理与云服务之间的通信。
- 我们为身份验证代理和云服务之间的 Websocket 连接添加了 DNS 缓存。
- 我们添加了定位云中的特定代理的功能，目的是针对代理连接性进行测试。

### <a name="fixed-issues"></a>修复的问题
- 版本 1.4.18.0 有一个 Bug，即 DSSO 的 PowerShell cmdlet 使用的是登录 Windows 凭据，而不是运行 ps 时提供的管理员凭据。 因此，无法通过 AADConnect 用户界面在多个林中启用 DSSO。 
- 我们进行了修复，允许通过 AADConnect 用户界面在所有林中同时启用 DSSO

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>版本状态
11/08/2019：已发布以供下载。 不通过自动升级提供。

>[!IMPORTANT]
>由于此版本的 Azure AD Connect 中的内部架构发生了更改，因此，如果你使用 MSOnline PowerShell 管理 AD FS 信任关系配置设置，则必须将 MSOnline PowerShell 模块更新为版本1.1.183.57 或更高版本

### <a name="fixed-issues"></a>修复的问题

此版本修复了已加入混合 Azure AD 的现有设备的问题。 此版本包含新的设备同步规则，用于纠正此问题。
请注意，此规则更改可能导致从 Azure AD 中删除过时设备。 这不应该担心，因为在进行条件访问授权期间，Azure AD 不使用这些设备对象。 对某些客户来说，通过此规则更改删除的设备数可能会超出删除阈值。 如果发现在 Azure AD 中删除设备对象时超出“导出删除阈值”，建议你允许删除操作完成。 [如何在删除操作数超出删除阈值时允许删除操作完成](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>版本状态
9/28/2019：已发布以便自动升级以选择租户。 不可供下载。

此版本修复了一个 Bug，该 Bug 的表现是：某些服务器在从旧版本自动升级到 1.4.18.0 时遇到自助式密码重置 (SSPR) 和密码写回问题。

### <a name="fixed-issues"></a>修复的问题

某些情况下，自动升级到版本 1.4.18.0 的服务器在完成升级后没有重新启用自助式密码重置和密码写回。 此自动升级版本修复了该问题并重新启用自助式密码重置和密码写回。

我们修复了同步错误压缩实用程序中无法正确处理代理项字符的 bug。

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>我们正在调查一个事件：某些客户在升级到此版 Azure AD Connect 后，其现有的已加入混合 Azure AD 的设备遇到问题。 我们建议已部署混合 Azure AD 加入的客户推迟升级到此版本，等待我们完全了解这些问题的根本原因并解决这些问题。 我们会尽快提供详细信息。

>[!IMPORTANT]
>使用此版 Azure AD Connect 时，某些客户可能会看到其部分或所有 Windows 设备从 Azure AD 中消失。 这不应该担心，因为在进行条件访问授权期间，Azure AD 不使用这些设备标识。 有关详细信息，请参阅[了解 Azure AD Connect 1.4. x 设备 disappearnce](reference-connect-device-disappearance.md)


### <a name="release-status"></a>版本状态
9/25/2019：仅为自动升级发布。

### <a name="new-features-and-improvements"></a>新增功能和改进
- 新的故障排除工具可帮助排查“用户未同步”、“组未同步”或“组成员未同步”问题。
- 在 AAD Connect 故障排除脚本中添加了对国家云的支持 
- 应通知客户 MIIS_Service 的已弃用 WMI 终结点现已删除。 现在，任何 WMI 操作应通过 PS cmdlet 完成。
- 通过重置 AZUREADSSOACC 对象中的约束委托来提高安全性
- 添加/编辑同步规则时，如果在规则中使用的任何属性位于未添加到连接器的连接器架构中，会自动将这些属性添加到连接器。 规则影响的对象类型也是如此。 如果在连接器中添加了任何内容，该连接器将标记为在下一个同步周期完全导入。
- 新的 Azure AD Connect 部署中不再支持使用企业或域管理员作为连接器帐户。 使用企业或域管理员作为连接器帐户的当前 AAD Connect 部署将不受此版本的影响。
- 在同步管理器中，创建/编辑/删除规则时将运行完全同步。 如果完全导入或完全同步操作将要运行，在发生任何规则更改时将弹出通知。
- 已将密码错误的缓解步骤添加到“连接器 > 属性 > 连接”页
- 在连接器属性页上添加了同步服务管理器弃用警告。 此警告通知用户应通过 Azure AD Connect 向导进行更改。
- 针对用户密码策略问题添加了新错误。
- 防止不当配置通过域和 OU 筛选器进行组筛选。 已筛选出输入组的域/OU 时，组筛选将显示一条错误消息，并在问题得到解决之前阻止用户继续操作。
- 用户无法再为 Synchronization Service Manager UI 中的 Active Directory 域服务或 Windows Azure Active Directory 创建连接器。
- 在 Synchronization Service Manager 中修复了自定义 UI 控件的辅助功能。
- 为 Azure AD Connect 中的所有登录方法启用了六个联合管理任务。  （以前，只有 "更新 AD FS TLS/SSL 证书" 任务可用于所有登录。）
- 添加了将登录方法从联合更改为 PHS 或 PTA 时的警告，指出所有 Azure AD 域和用户将转换为托管身份验证。
- 从“重置 Azure AD 和 AD FS 信任”任务中删除了令牌签名证书，并添加了单独的子任务来更新这些证书。
- 添加了名为 "管理证书" 的新联合管理任务，其中包含用于更新 AD FS 场的 TLS 或令牌签名证书的子任务。
- 添加了名为“指定主服务器”的新联合管理子任务，该任务可让管理员为 AD FS 场指定新的主服务器。
- 添加了名为“管理服务器”的新联合管理任务，其中包含用于部署 AD FS 服务器、部署 Web 应用程序代理服务器和指定主服务器的子任务。
- 添加了名为“查看联合配置”的新联合管理任务，它会显示当前 AD FS 设置。  （由于添加了此任务，已从“查看解决方案”页中删除了 AD FS 设置。）

### <a name="fixed-issues"></a>修复的问题
- 解决了当接管相应联系人对象的用户对象存在自我引用情况（例如，用户是其自己的管理员）时出现同步错误的问题。
- “帮助”弹出窗口现在会在键盘焦点上显示。
- 在自动升级期间，如果任何有冲突的应用运行了 6 小时，则会将其终止，然后继续升级。
- 将客户在选择目录扩展时可选择的属性数限制为每个对象 100 个属性。 这可以防止在导出过程中出错，因为 Azure 的最大限制为每个对象最多有 100 个扩展属性。
- 修复了一个 bug，使 AD 连接脚本更可靠
- 修复了一个 bug，使得通过现有命名管道 WCF 服务在计算机上安装的 AADConnect 更可靠。
- 改进了在初始安装后不允许 ADSync 服务启动的组策略的诊断和故障排除。
- 修复了一个 bug：Windows 计算机的显示名称拼写错误。
- 修复了一个 bug：Windows 计算机的 OS 类型拼写错误。
- 修复了一个 bug：非 Windows 10 计算机意外同步。 请注意，此项更改的影响是，以前已同步的非 Windows 10 计算机现在将被删除。 这不会影响任何功能，因为 Windows 计算机同步仅用于混合 Azure AD 域加入，而后者仅适用于 Windows 10 设备。
- 已将多个新的（内部）cmdlet 添加到 ADSync PowerShell 模块。


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>将 Azure AD Connect 从早期版本升级到 1.3.21.0 存在一个已知问题，即，即使 Azure AD Connect 升级成功，O365 门户也不反映已更新版本。
>
> 若要解决此问题，你需要导入**AdSync**模块，然后 `Set-ADSyncDirSyncConfiguration` 在 Azure AD Connect 服务器上运行 PowerShell cmdlet。  可以使用以下步骤：
>
>1. 在管理员模式下打开 PowerShell。
>2. 运行 `Import-Module "ADSync"`。
>3. 运行 `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`。
 
### <a name="release-status"></a>版本状态 

05/14/2019：已发布以供下载

### <a name="fixed-issues"></a>修复的问题 

- 修复了 Microsoft Azure Active Directory Connect 生成1.3.20.0 中存在的特权提升漏洞。  在某些情况下，此漏洞可能允许攻击者在特权帐户的上下文中执行两个 PowerShell cmdlet，并执行特权操作。  此安全更新通过禁用这些 cmdlet 来解决此问题。 有关详细信息，请参阅[安全更新](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)。

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>版本状态 

04/24/2019：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进 

- 添加了对域刷新的支持 
- “Exchange 邮件公用文件夹”功能已推出正式版 
- 改进了发生服务故障时在向导中处理错误的方法 
- 在连接器属性页的 Synchronization Service Manager UI 中添加了警告链接。 
- “统一组写回”功能现已推出正式版 
- 改进了当 DC 缺少 LDAP 控制措施时显示的 SSPR 错误消息 
- 添加了在安装期间出现的 DCOM 注册表错误的诊断方法  
- 改进了 PHS RPC 错误跟踪 
- 允许从子域添加 EA 凭据 
- 允许在安装期间输入数据库名称（默认名称为 ADSync）
- 升级到了 ADAL 3.19.8 以便对 Ping 执行 WS-Trust 修复，并添加了对新 Azure 实例的支持 
- 修改了组同步规则，以便将声明所需的 samAccountName、DomainNetbios 和 DomainFQDN 传送到云中 
- 已修改默认同步规则处理–[在此处](how-to-connect-fix-default-rules.md)阅读详细信息。
- 添加了一个作为 Windows 服务运行的新代理。 此代理名为“管理代理”，可用于对 Azure AD Connect 服务器进行更深入的远程诊断，以帮助 Microsoft 工程师在收到支持案例时进行故障排除。 默认情况下不会安装并启用此代理。  有关如何安装并启用此代理的详细信息，请参阅[什么是 Azure AD Connect 管理员代理？](whatis-aadc-admin-agent.md)。 
- 更新了最终用户许可协议 (EULA) 
- 为使用 AD FS 作为登录类型的部署添加了自动升级支持。  借助此新增功能，在升级过程中不再需要更新 AD FS Azure AD 信赖方信任。 
- 添加了一个 Azure AD 信任管理任务，该任务提供两个选项：分析/更新信任和重置信任。 
- 更改了 AD FS Azure AD 信赖方信任行为，使其始终使用 -SupportMultipleDomain 开关（包括信任和 Azure AD 域更新）。 
- 更改了“安装新 AD FS 场”的行为：删除了使用预装证书的选项，使该操作要求提供 .pfx 证书。
- 更新了“安装新 AD FS 场”工作流，使其只允许部署 1 个 AD FS 和 1 个 WAP 服务器。  完成初始安装后，将配置所有附加服务器。 

### <a name="fixed-issues"></a>修复的问题 

- 修复了 SQL 重新连接 ADSync 服务逻辑的问题 
- 修复后允许使用空的 SQL AOA DB 执行全新安装 
- 修复了 PS 权限脚本，以细化 GWB 权限 
- 修复了 LocalDB 出现的 VSS 错误  
- 修复了当对象类型不在范围内时出现的误导性错误消息 
- 更正了以下问题：在服务器上安装 Azure AD PowerShell 可能会导致某个程序集与 Azure AD Connect 相冲突。 
- 修复了在 Synchronization Service Manager UI 中更新连接器凭据时，暂存服务器上出现的 PHS Bug。 
- 修复了一些内存泄漏问题 
- 其他自动升级修复 
- 对导出和未确认的导入处理进行了其他修复 
- 修复了处理域和 OU 筛选中的反斜杠时存在的 bug 
- 修复了以下问题：ADSync 服务需要 2 分钟以上才能停止，导致升级时出现问题。 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>版本状态

12/18/2018：已发布以供下载

### <a name="fixed-issues"></a>修复的问题

此版本更新 Azure AD Connect 随附的非标准连接器（例如，泛型 LDAP 连接器和泛型 SQL 连接器）。 有关适用连接器的详细信息，请参阅[连接器版本发行历史记录](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)中的版本 1.1.911.0。


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>版本状态
12/11/2018：已发布以供下载

### <a name="fixed-issues"></a>修复的问题
此修补程序版本允许用户在启用设备写回时在指定林中为 RegisteredDevices 容器选择目标域。  在包含新的设备选项功能的旧版本 (1.1.819.0 – 1.2.68.0) 中，RegisteredDevices 容器位置仅限林根，不允许子域。  此限制仅体现在新部署中 - 就地升级不受影响。  

如果已将包含已更新设备选项功能的版本部署到新的服务器且已启用设备写回，则在不希望容器位于林根中的情况下，需手动指定容器的位置。  为此，需禁用设备写回，然后重新启用它，以便在“写回林”页上指定容器位置。



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>版本状态 

11/30/2018：已发布以供下载

### <a name="fixed-issues"></a>修复的问题

此修补程序版本修复了一个冲突，出现该冲突时，由于同步服务器上有一个单独存在的 MSOnline PowerShell 库模块，可能会发生身份验证错误。



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>版本状态 

2018/11/19：已发布供下载

### <a name="fixed-issues"></a>修复的问题

此修补程序版本修复了之前版本的回归问题：在 Windows Server 2008/R2 上使用 ADDS 域控制器时，密码写回失败。

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>版本状态 

10/25/2018：已发布供下载

 
### <a name="new-features-and-improvements"></a>新增功能和改进 


- 更改了属性写回的功能，以确保托管的语音邮件可按预期方式工作。  在某些情况下，Azure AD 在使用 null 值写回期间，会覆盖 msExchUcVoicemailSettings 属性。  如果未设置云值，Azure AD 现在不再会清除此属性的本地值。
- 在 Azure AD Connect 向导中添加了诊断，用于调查和识别 Azure AD 连接问题。 也可以使用 AdSyncAzureServiceConnectivity Cmdlet 直接通过 PowerShell 运行相同的诊断。 
- 在 Azure AD Connect 向导中添加了诊断，用于调查和识别 AD 连接问题。 也可以使用 ADConnectivityTools PowerShell 模块中的 ConnectivityValidation 函数直接通过 PowerShell 运行相同的诊断。  有关详细信息，请参阅[什么是 ADConnectivityTool PowerShell 模块？](how-to-connect-adconnectivitytools.md)
- 为混合 Azure Active Directory Join 和设备写回添加了 AD 架构版本预先检查 
- 已将目录扩展页面属性搜索更改为不区分大小写。
-   添加了对 TLS 1.2 的完整支持。 此版本支持所要禁用的其他所有协议，安装 Azure AD Connect 的计算机上只会启用 TLS 1.2。  有关详细信息，请参阅[对 Azure AD Connect 强制实施 TLS 1.2](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>修复的问题   

- 修复了使用 SQL Always On 时 Azure AD Connect 升级失败的 bug。 
- 修复了一个 bug，现在可以正常分析包含正斜杠的 OU 名称。 
- 修复了在过渡模式下执行全新安装时禁用直通身份验证的问题。 
- 修复了在运行故障排除工具时阻止加载 PowerShell 模块的 bug 
- 修复了阻止客户在主机名第一个字符中使用数字值的 bug。 
- 修复了 Azure AD Connect 允许选择无效分区和容器的 bug 
- 修复了启用桌面 SSO 时出现“密码无效”错误消息的问题。 
- AD FS 信任管理的各项 Bug 修复  
- 配置设备写回时 - 修复了架构检查，现在可以查找 msDs-DeviceContainer 对象类（已在 WS2012 R2 中引入）

 
## <a name="118820"></a>1.1.882.0  

2018/9/7：发布可供下载版本，将不发布可自动升级的版本 

### <a name="fixed-issues"></a>修复的问题  

若已为 ADSync DB 配置 SQL Always On 可用性组，则 Azure AD Connect 升级将失败。 此修补程序将解决此问题并帮助成功进行升级。 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>版本状态

2018/8/21：已发布，用于下载和自动升级。 

### <a name="new-features-and-improvements"></a>新增功能和改进

- Azure AD Connect 中的 Ping Federate 集成现已公开发布。 [深入了解如何将 Azure AD 与 Ping Federate 联合](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Azure AD Connect 现在每次更新时都可在 AD FS 中创建 Azure AD 信任的备份，并将其存储在单独的文件中以便轻松还原（如果需要）。 [详细了解 Azure AD Connect 中的新增功能和 Azure AD 信任管理](https://aka.ms/fedtrustinaadconnect)。
- 新的故障排除工具有助于解决更改主要电子邮件地址和隐藏全局地址列表中的帐户的问题
- 已更新 Azure AD Connect，以包括最新版 SQL Server 2012 本机客户端
- 如果在“更改用户登录”任务中将用户登录切换为“密码哈希同步”或“直通身份验证”，将默认启用“无缝单一登录”复选框。
- 添加了对 Windows Server Essentials 2019 的支持
- Azure AD Connect Health 代理已更新到最新版 3.1.7.0
- 升级过程中，如果安装程序检测到对默认同步规则的更改，覆盖经修改的规则之前系统将对管理员进行警告。 这将允许用户采取纠正措施并稍后继续操作。 旧行为：如果存在任何经修改的现成规则，则手动升级将覆盖这些规则，而不会向用户发出任何警告，并且在不通知用户的情况下禁用同步计划程序。 新行为：覆盖经修改的现成同步规则之前，系统将警告用户。 用户可以选择停止升级过程，并在采取纠正措施后继续操作。
- 更好地处理 FIPS 符合性问题，提供针对符合 FIPS 的环境中的 MD5 哈希生成的错误消息和有关此问题解决办法的文档链接。
- UI 改进以改进向导中的联合任务，这些任务现位于单独的联合子组中。 
- 现在，所有附加联合任务集中在单个子菜单下，易于使用。
- 包含新的 AD 权限函数的新改进的 ADSyncConfig Posh 模块 (AdSyncConfig.psm1) 从旧的 ADSyncPrep.psm1 移出（可能很快弃用）

### <a name="fixed-issues"></a>修复的问题 

- 修复了以下 bug：在升级到 .NET 4.7.2 之后，AAD Connect 服务器显示 CPU 使用率高
- 修复了以下 bug：针对自动解决的 SQL 死锁问题间歇性生成错误消息
- 修复了同步规则编辑器和 Sync Service Manager 的多个辅助功能问题  
- 修复了以下 bug：Azure AD Connect 无法获取注册表设置信息
- 修复了以下 bug：用户在向导中前进/后退时会出现问题
- 修复了以下 bug：由于向导中多线程处理不正确而导致错误
- 当“组同步筛选”页在解析安全组时遇到 LDAP 错误，Azure AD Connect 现在会返回全保真度异常。  引用异常的根本原因仍未知，并且将被其他 bug 解决。
-  修复了 STK 和 NGC 键（WHfB 的用户/设备对象的 ms-DS-KeyCredentialLink 属性）的权限设置出错这一 bug。     
- 修复了以下 bug：错误调用“Set-ADSyncRestrictedPermissions”
-  添加对 AADConnect 安装向导中组写回权限授予的支持
- 如果将登录方法从“密码哈希同步”更改为“AD FS”，不会禁用“密码哈希同步”。
- 添加了对 AD FS 配置中的 IPv6 地址的验证
- 更新了通知消息，用于通知已存在一个现有配置。
- 设备写回未能检测到不受信任林中的容器。 已经对此进行更新，以提供更好的错误消息和相应文档链接
- 取消选中 OU 后，该 OU 对应的同步/写回出现一般同步错误。 已经对此进行更改，以创建更易于理解的错误消息。

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>版本状态

5/14/2018：已发布，用于自动升级和下载。

### <a name="new-features-and-improvements"></a>新增功能和改进

新增功能和改进

- 此版本包含 Azure AD Connect 中 PingFederate 集成的公共预览。 借助此版本，客户可以轻松可靠地将 Azure Active Directory 环境配置为，使用 PingFederate 作为联合身份验证提供程序。 若要了解有关如何使用此新功能的详细信息，请访问我们的[在线文档](plan-connect-user-signin.md#federation-with-pingfederate)。 
- 更新了 Azure AD Connect 向导疑难解答实用工具，现在可以分析更多错误方案，如链接邮箱和 AD 动态组。 在[此处](tshoot-connect-objectsync.md)阅读有关疑难解答实用工具的详细信息。
- 设备写回配置现在仅在 Azure AD Connect 向导中进行管理。
- 添加了名为 ADSyncTools.psm1 的新 PowerShell 模块，可用于 SQL 连接问题故障排除和各种其他疑难解答实用工具。 在[此处](tshoot-connect-tshoot-sql-connectivity.md)阅读有关 ADSyncTools 模块的详细信息。 
- 添加了新的“配置设备选项”任务。 可使用该任务来配置以下两个操作： 
  - **混合 Azure AD 加入**：如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备同时加入到本地 Active Directory 和 Azure Active Directory。
  - **设备写回**：设备写回用于启用基于设备的条件访问 AD FS （2012 R2 或更高版本）受保护的设备

    >[!NOTE] 
    > - 通过自定义同步选项启用设备写回的选项将灰显。 
    > -  适用于 ADPrep 的 PowerShell 模块在此版本中弃用。



### <a name="fixed-issues"></a>修复的问题 

- 此版本将 SQL Server Express 安装更新为 SQL Server 2012 SP4，该版本及其他一些版本针对多个安全漏洞提供修补程序。  有关 SQL Server 2012 SP4 的详细信息，请参阅[此处](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information)。
- 同步规则处理：如果父同步规则不再适用，应取消应用没有联接条件的出站联接同步规则
- 多个可访问性修补程序已应用于 Synchronization Service Manager UI 和同步规则编辑器
- Azure AD Connect 向导：Azure AD Connect 位于工作组中时，创建 AD 连接器帐户出错
- Azure AD Connect 向导：AD 域和 Azure AD 验证域存在任何不匹配时，在 Azure AD 登录页面上显示验证复选框
- 自动升级 PowerShell 修补程序，以在尝试自动升级后的某些情况下正确设置自动升级状态。
- Azure AD Connect 向导：更新遥测以捕获之前缺失的信息
- Azure AD Connect 向导：当使用**更改用户登录**任务从 AD FS 切换到直通身份验证时，已进行以下更改：
    - 在我们将域从联盟域转换为托管域之前，直通身份验证代理已安装在 Azure AD Connect 服务器上，并且直通身份验证功能处于已启用状态。
    - 用户不再从联盟用户转换为托管用户。 只有域会被转换。
- Azure AD Connect 向导：当用户 UPN 更新 ' 特殊字符正则表达式以支持特殊字符时，AD FS 多域正则表达式不正确
- Azure AD Connect 向导：在无更改时删除虚假的“配置源定位点属性”消息 
- Azure AD Connect 向导：对双联合方案的 AD FS 支持
- Azure AD Connect 向导：在将托管域转换为联合域时，AD FS 声明未针对添加的域进行更新
- Azure AD Connect 向导：在检测已安装的程序包期间，我们发现过时的 Dirsync/Azure AD Sync/Azure AD Connect 相关产品。 我们现在将尝试卸载过时的产品。
- Azure AD Connect 向导：更正安装传递身份验证代理失败时的错误消息映射
- Azure AD Connect 向导：从域 OU 筛选页面删除“配置”容器
- 同步引擎安装：移除偶尔因同步引擎安装 msi 而失败的不必要的旧逻辑
- Azure AD Connect 向导：修复密码哈希同步的可选功能页面中的弹出帮助文本
- 同步引擎运行时：修复 CS 对象具有导入的删除并且同步规则尝试重新预配对象的情况。
- 同步引擎运行时：为导入错误事件日志添加联机连接疑难解答指南帮助链接
- 同步引擎运行时：枚举连接器时减少同步计划程序的内存使用量
- Azure AD Connect 向导：解决解析无 AD 读取特权的自定义同步服务帐户时出现的问题
- Azure AD Connect 向导：改进对域和 OU 筛选选择的日志记录
- Azure AD Connect 向导：AD FS 将默认声明添加到为 MFA 方案创建的联合信任
- Azure AD Connect 向导：AD FS 部署 WAP：添加服务器时未能使用新证书
- Azure AD Connect 向导：未针对域初始化 onPremCredentials 时出现 DSSO 异常 
- 优先从活动用户对象中流动 AD distinguishedName 属性。
- 修复了以下显示错误：第一个 OOB 同步规则的优先级设置为 99，而不是 100



## <a name="117510"></a>1.1.751.0
状态：2018 年 4 月 12 日发布，仅供下载

>[!NOTE]
>此版本是 Azure AD Connect 的修补程序

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同步
#### <a name="fixed-issues"></a>修复的问题
修复了以下问题：自动发现 Azure 实例有时会对中国区租户无效。  

### <a name="ad-fs-management"></a>AD FS 管理
#### <a name="fixed-issues"></a>修复的问题

配置重试逻辑中存在一个问题，该问题将导致一个 ArgumentException，指出“已添加了具有相同键的项”。  这会导致所有重试操作失败。

## <a name="117500"></a>1.1.750.0
状态 3/22/2018：已发布，用于自动升级和下载。
>[!NOTE]
>完成到此新版本的升级以后，将会自动触发针对 Azure AD 连接器的完全同步和完全导入，以及针对 AD 连接器的完全同步。 由于这可能需要一些时间（具体取决于 Azure AD Connect 环境的大小），因此请确保已采取必要的支持措施，否则需推迟升级，直至找到合适的升级时间。

>[!NOTE]
>“对于部署了高于 1.1.524.0 的版本的部分租户，自动升级功能错误地被禁用了。 若要确保你的 Azure AD Connect 实例依然可以进行自动升级，请运行以下 PowerShell cmdlet：“Set-ADSyncAutoUpgrade -AutoupGradeState Enabled”


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>修复的问题

* 如果自动升级状态设置为“已暂停”，则 Set-ADSyncAutoUpgrade cmdlet 以前会阻止自动升级。 此功能现已更改为，不阻止自动升级未来版本。
* 将“用户登录”**** 页选项“密码同步”更改为了“密码哈希同步”。  Azure AD Connect 同步密码哈希值（而不是密码），因此这与实际发生的情况一致。  有关详细信息，请参阅[利用 Azure AD Connect 同步实现密码哈希同步](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
状态：已分发给选定客户

>[!NOTE]
>完成到此新版本的升级以后，将会自动触发针对 Azure AD 连接器的完全同步和完全导入，以及针对 AD 连接器的完全同步。 由于这可能需要一定的时间（具体取决于 Azure AD Connect 环境的大小），因此请确保已采取必要的支持措施，否则需推迟升级，直至找到合适的升级时间。

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>修复的问题
* 修复了在切换到下一页时，“分区筛选”页的后台任务的计时窗口问题。

* 修复了在 ConfigDB 自定义操作过程中导致访问冲突的 Bug。

* 修复了 Bug，因此可以从 SQL 连接超时恢复。

* 修复了带 SAN 通配符的证书无法通过先决条件检查的 Bug。

* 修复了在 Azure AD 连接器导出过程中导致 miiserver.exe 崩溃的 Bug。

* 修复了在运行 Azure AD Connect 向导来更改配置时，可以通过不断地尝试密码登录 DC 的 Bug。


#### <a name="new-features-and-improvements"></a>新增功能和改进

* 为一般数据保护条例 (GDPR) 添加隐私设置。  有关详细信息，请参阅[此处](reference-connect-user-privacy.md)的文章。

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* 应用程序遥测 - 管理员可以随意切换此类数据的开/关设置

* Azure AD 运行状况数据 - 管理员必须访问运行状况门户才能控制其运行状况设置。
   等到服务策略更改以后，代理就会读取并强制实施它。

* 添加了设备写回配置操作以及用于页面初始化的进度栏

* 改进了 HTML 报表的常规诊断功能以及 ZIP-Text/HTML 报表的完整数据收集功能

* 提高了自动升级的可靠性并增加了更多的遥测，确保可以确定服务器的运行状况

* 限制提供给以 AD 连接器帐户为基础的特权帐户的权限

  * 进行全新安装时，向导会限制特权帐户拥有的针对 MSOL 帐户的权限（前提是 MSOL 帐户已创建）。

这些更改将针对以下事项：
1. 快速安装
2. 用于自动创建帐户的自定义安装
3. 更改了安装程序，因此在进行 Azure AD Connect 的全新安装时，不需要 SA 权限

* 添加了新的实用程序，用于排查特定对象的同步问题。 该实用程序位于 Azure AD Connect 向导的“排查其他任务的问题”的“排查对象同步问题”选项下。 目前，该实用程序用于检查以下问题：

  * Azure AD 租户中的已同步用户对象和用户帐户之间出现 UserPrincipalName 不匹配的情况。
  * 是否已通过域筛选将对象从同步中筛选出来
  * 是否已通过组织单位 (OU) 筛选将对象从同步中筛选出来

* 添加了一个新的实用程序，用于同步当前的密码哈希，该哈希存储在针对特定用户帐户的本地 Active Directory 中。

该实用程序不需要更改密码。 该实用程序位于 Azure AD Connect 向导的“排查其他任务的问题”的“排查密码哈希同步问题”选项下。






## <a name="116540"></a>1.1.654.0
状态：2017 年 12 月 12 日

>[!NOTE]
>此版本是 Azure AD Connect 安全相关修补程序

### <a name="azure-ad-connect"></a>Azure AD Connect
Azure AD Connect 版本 1.1.654.0（以及更高版本）中已添加了一项改进，以确保当 Azure AD Connect 创建 AD DS 帐户时会自动应用[锁定对 AD DS 帐户的访问](#lock)部分下所述的建议权限更改。 

- 设置 Azure AD Connect 时，安装管理员可提供现有的 AD DS 帐户，也可以让 Azure AD Connect 自动创建帐户。 权限更改将自动应用到在设置期间由 Azure AD Connect 创建的 AD DS 帐户。 它们不适用于安装管理员所提供的现有 AD DS 帐户。
- 对于从早期版本的 Azure AD Connect 升级到 1.1.654.0（或更高版本）的客户，权限更改将不会以追溯的方式应用于升级前创建的现有 AD DS 帐户。 它们仅适用于升级后创建的新 AD DS 帐户。 在添加要被同步到 Azure AD 的新 AD 林时会发生这种情况。

>[!NOTE]
>此版本仅删除了 Azure AD Connect 新安装的漏洞，这些安装的服务帐户是由安装进程创建的。 对于现有安装，或者在自己提供帐户的情况下，你应该确保此漏洞不存在。

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>锁定对 AD DS 帐户的访问
通过在本地 AD 中实现以下权限更改来锁定对 AD DS 帐户的访问：  

*   在指定对象上禁用继承
*   删除特定对象上的所有 ACE，特定于 SELF 的 ACE 除外。 当涉及到 SELF 时，我们希望保持默认权限不变。
*   分配以下特定权限：

类型     | 名称                          | 访问               | 应用于
---------|-------------------------------|----------------------|--------------|
Allow    | SYSTEM                        | 完全控制         | 此对象  |
Allow    | Enterprise Admins             | 完全控制         | 此对象  |
Allow    | Domain Admins                 | 完全控制         | 此对象  |
Allow    | Administrators                | 完全控制         | 此对象  |
Allow    | 企业域控制器 | 列出内容        | 此对象  |
Allow    | 企业域控制器 | 读取所有属性  | 此对象  |
Allow    | 企业域控制器 | 读取权限     | 此对象  |
Allow    | 经过身份验证的用户           | 列出内容        | 此对象  |
Allow    | 经过身份验证的用户           | 读取所有属性  | 此对象  |
Allow    | 经过身份验证的用户           | 读取权限     | 此对象  |

若要增强 AD DS 帐户的设置，可以运行[此 PowerShell 脚本](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978)。 PowerShell 脚本将为 AD DS 帐户分配上述的权限。

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell 脚本增强预先存在的服务帐户

若要使用 PowerShell 脚本将这些设置应用到预先存在的 AD DS 帐户（这些帐户由组织提供或由先前安装的 Azure AD Connect 创建），请从上面提供的链接下载脚本。

##### <a name="usage"></a>用法：

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

其中 

**$ObjectDN** = 需要增强权限的 Active Directory 帐户。

**$Credential** = 管理员凭据，拥有限制 $ObjectDN 帐户权限的必要特权。 企业或域管理员通常拥有这些特权。 使用管理员帐户的完全限定的域名以避免查找帐户失败。 示例：contoso.com\admin。

>[!NOTE] 
>$credential.UserName 应为 FQDN\username 格式。 示例：contoso.com\admin 

##### <a name="example"></a>示例：

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>此漏洞曾用于未经授权的访问？

若要查看此漏洞是否曾用于入侵 Azure AD Connect 配置，你应该验证服务帐户的最后一次密码重置日期。  如果存在非预期的时间戳，应通过事件日志进一步调查密码重置事件。

有关详细信息，请参阅 [Microsoft 安全公告 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
状态：2017 年 10 月 27 日

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>修复的问题
* 修复了 Azure AD Connect 与 Azure AD Connect Health 代理（用于同步）之间存在的版本兼容性问题。 此问题会影响要执行 Azure AD Connect 就地升级到版本 1.1.647.0，但当前 Health 代理版本为 3.0.127.0 的用户。 升级之后，Health 代理不再能够将有关 Azure AD Connect 同步服务的运行状况数据发送到 Azure AD Health 服务。 通过此修复，在 Azure AD Connect 就地升级过程中将安装 Health 代理版本 3.0.129.0。 Health 代理版本 3.0.129.0 与 Azure AD Connect 版本 1.1.649.0 没有兼容性问题。


## <a name="116470"></a>1.1.647.0
状态：2017 年 10 月 19 日

> [!IMPORTANT]
> Azure AD Connect 版本 1.1.647.0 与 Azure AD Connect Health 代理（用于同步）版本 3.0.127.0 之间存在已知的兼容性问题。 此问题会阻止 Health 代理向 Azure AD Health 服务发送有关 Azure AD Connect 同步服务的运行状况数据（包括对象同步错误和运行历史记录数据）。 将 Azure AD Connect 部署手动升级到版本 1.1.647.0 之前，请验证 Azure AD Connect 服务器上是否安装了最新版本的 Azure AD Connect Health 代理。 为此，可以转到 *"控制面板" → "添加/删除程序"* ，查找应用程序*Microsoft Azure AD 连接运行状况代理进行同步*"。如果它的版本为3.0.127.0，则建议您在升级前等待下一 Azure AD Connect 版本可用。 如果 Health 代理版本不是 3.0.127.0，则可以继续进行手动就地升级。 请注意，此问题不会影响交叉升级，也不影响执行 Azure AD Connect 全新安装的客户。
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>修复的问题
* 修复了 Azure AD Connect 向导中的“更改用户登录”任务：**

  * 如果存在一个**已启用**密码同步的现有 Azure AD Connect 部署，并尝试将用户登录方法设置为“直通身份验证”，则会发生此问题。** 在应用更改之前，向导会错误地显示“禁用密码同步”提示。** 但是，在应用更改之后，密码同步仍保持启用状态。 通过这项修复，向导不再显示该提示。

  * 根据设计，在使用“更改用户登录”任务更新用户登录方法时，向导不会禁用密码同步。** 这是为了避免干扰想要保留密码同步的客户，即使他们启用直通身份验证或联合身份验证作为其主要用户登录方法。
  
  * 如果想要在更新用户登录方法后禁用密码同步，必须执行向导中的“自定义同步配置”任务。** 导航到“可选功能”页后，取消选中“密码同步”选项。****
  
  * 请注意，如果尝试启用/禁用无缝单一登录，也会发生同样的问题。 具体而言，此时某个现有的 Azure AD Connect 部署启用了密码同步，同时用户登录方法已配置为“直通身份验证”。** 使用“更改用户登录”任务尝试选中/取消选中“启用无缝单一登录”选项，同时将用户登录方法保持配置为“直通身份验证”。**** 在应用更改之前，向导会错误地显示“禁用密码同步”提示。** 但是，在应用更改之后，密码同步仍保持启用状态。 通过这项修复，向导不再显示该提示。

* 修复了 Azure AD Connect 向导中的“更改用户登录”任务：**

  * 如果存在一个**已禁用**密码同步的现有 Azure AD Connect 部署，并尝试将用户登录方法设置为“直通身份验证”，则会发生此问题。** 在应用更改后，向导会同时启用直通身份验证和密码同步。 通过这项修复，向导不再启用密码同步。

  * 过去，密码同步是启用直通身份验证的先决条件。 将用户登录方法设置为“直通身份验证”时，向导会同时启用直通身份验证和密码同步。** 最近，已去除“密码同步”这项先决条件。 Azure AD Connect 版本 1.1.557.0 中对 Azure AD Connect 做了更改，在将用户登录方法设置为“直通身份验证”时，不会启用密码同步。** 但是，该项更改只会应用到 Azure AD Connect 安装。 通过这项修复，相同的更改也会应用到“更改用户登录”任务。**
  
  * 请注意，如果尝试启用/禁用无缝单一登录，也会发生同样的问题。 具体而言，此时某个现有的 Azure AD Connect 部署禁用了密码同步，同时用户登录方法已配置为“直通身份验证”。** 使用“更改用户登录”任务尝试选中/取消选中“启用无缝单一登录”选项，同时将用户登录方法保持配置为“直通身份验证”。**** 应用这项更改后，向导会启用密码同步。 通过这项修复，向导不再启用密码同步。 

* 修复了一个导致 Azure AD Connect 升级失败并出现错误“无法升级同步服务”的问题。** 此外，在出现事件错误“服务无法启动，因为数据库的版本比所安装的二进制文件的版本更新”时，同步服务不再能够启动。** 当执行升级的管理员对 Azure AD Connect 所用的 SQL 服务器没有 sysadmin 特权时，将会出现此问题。 通过这项修复，Azure AD Connect 只要求管理员在升级期间对 ADSync 数据库拥有 db_owner 特权。

* 修复了一个会对已启用[无缝单一登录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)的客户造成影响的 Azure AD Connect 升级问题。 升级 Azure AD Connect 之后，Azure AD Connect 向导中会错误地将无缝单一登录显示为已禁用，即使该功能保持已启用状态且完全正常。 通过这项修复，该功能现在会正确地在向导中显示为已启用。

* 修复了一个导致 Azure AD Connect 向导始终在“已准备好配置”页上显示“配置源定位点”提示（即使未做出与源定位点相关的任何更改）的问题。****

* 在执行 Azure AD Connect 的手动就地升级时，客户必须提供相应 Azure AD 租户的全局管理员凭据。 过去，即使全局管理员凭据属于其他 Azure AD 租户，也能继续升级。 尽管升级看上去已成功完成，但某些配置不会在升级时正确保留。 待此次变更生效后，如果提供的凭据与 Azure AD 租户不一致，向导会阻止继续升级。

* 删除了在开始手动升级时不必要地重启 Azure AD Connect Health 服务的多余逻辑。


#### <a name="new-features-and-improvements"></a>新增功能和改进
* 添加了逻辑来简化在 Microsoft 德国云中设置 Azure AD Connect 所要执行的步骤。 过去，我们必须按此文中所述更新 Azure AD Connect 服务器上的特定注册表项，才能让 Azure AD Connect 在 Microsoft 德国云中正常工作。 现在，Azure AD Connect 可以根据安装期间提供的全局管理员凭据，自动检测租户是否在 Microsoft 德国云中。

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> 注意：同步服务提供一个 WMI 接口让客户开发自己的自定义计划程序。 此接口现已弃用，并会从 2018 年 6 月 30 日之后交付的后续 Azure AD Connect 版本中删除。 想要自定义同步计划的客户应使用[内置计划程序](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler)。

#### <a name="fixed-issues"></a>修复的问题
* 当 Azure AD Connect 向导创建从本地 Active Directory 同步更改所需的 AD 连接器帐户时，不会正确地向该帐户分配读取 PublicFolder 对象所需的权限。 此问题会影响“快速”安装和“自定义”安装。 此项更改修复了该问题。

* 修复了一个导致通过 Windows Server 2016 运行 Azure AD Connect 向导的管理员无法正常查看其故障排除页的问题。

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 使用 Azure AD Connect 向导故障排除页排查密码同步问题时，故障排除页现在会返回特定于域的状态。

* 过去，如果尝试启用密码哈希同步，Azure AD Connect 不会验证 AD 连接器帐户是否拥有从本地 AD 同步密码哈希所需的权限。 现在，Azure AD Connect 向导会验证权限，并在 AD 连接器帐户没有足够的权限时发出警告。

### <a name="ad-fs-management"></a>AD FS 管理
#### <a name="fixed-issue"></a>修复的问题
* 修复了与[将 ms-DS-ConsistencyGuid 用作源定位点](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能的用法相关的问题。 此问题会影响已将“使用 AD FS 进行联合身份验证”配置为用户登录方法的客户。** 执行向导中的“配置源定位点”任务时，Azure AD Connect 会改用 *ms-DS-ConsistencyGuid 作为 immutableId 的源属性。** 在应用此项更改的过程中，Azure AD Connect 会尝试更新 AD FS 中 ImmutableId 的声明规则。 但是，由于 Azure AD Connect 无法提供配置 AD FS 所需的管理员凭据，此步骤失败。 通过这项修复，在执行“配置源定位点”任务时，Azure AD Connect 现在会提示输入 AD FS 的管理员凭据。**



## <a name="116140"></a>1.1.614.0
状态：2017 年 9 月 5 日

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>已知问题
* 有个已知问题会导致 Azure AD Connect 升级失败并出现错误“无法升级同步服务”的问题。** 此外，在出现事件错误“服务无法启动，因为数据库的版本比所安装的二进制文件的版本更新”时，同步服务不再能够启动。** 当执行升级的管理员对 Azure AD Connect 所用的 SQL 服务器没有 sysadmin 特权时，将会出现此问题。 Dbo 权限是不够的。

* Azure AD Connect 升级存在已知问题，会对已启用[无缝单一登录](how-to-connect-sso.md)的客户造成影响。 升级 Azure AD Connect 后，功能会在向导中显示为已禁用，即使功能为启用状态也是如此。 未来的发布中将提供对此问题的修复。 担心此显示问题的客户可以手动修复此问题，方法是在向导中启用无缝单一登录。

#### <a name="fixed-issues"></a>修复的问题
* 修复了以下问题：启用[将 ms-DS-ConsistencyGuid 用作源定位点](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能时，Azure AD Connect 无法更新本地 AD FS 中的声明规则。 如果尝试为将 AD FS 配置为登录方法的现有 Azure AD Connect 部署启用此功能，就会出现此问题。 之所以出现此问题是因为，向导在尝试更新 AD FS 中的声明规则前未提示输入 ADFS 凭据。
* 修复了本地 AD 林禁用 NTLM 后导致 Azure AD Connect 无法安装的问题。 此问题的起因是：创建 Kerberos 身份验证所需的安全上下文时，Azure AD Connect 向导未提供完全限定的凭据。 这将导致 Kerberos 身份验证失败并且 Azure AD Connect 向导将回退到使用 NTLM。

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>修复的问题
* 修复了未填充标记属性时无法创建新同步规则的问题。
* 修复了即使 Kerberos 可用时，仍导致 Azure AD Connect 使用 NTLM 连接到本地 AD 进行密码同步的问题。 如果本地 AD 拓扑有一个或多个通过备份还原的域控制器，就会出现此问题。
* 修复了导致升级后出现不必要完整同步步骤的问题。 一般情况下，如果现成的同步规则发生了更改，则升级后需要运行完整同步步骤。 此问题的起因是：更改检测逻辑中出现错误，当遇到具有换行字符的同步规则表达式时，此错误导致错误地检测到更改。 已向同步规则表达式中插入了换行符来提升可读性。
* 修复了可能导致自动升级后 Azure AD Connect 服务器无法正常工作的问题。 此问题会影响 1.1.443.0 版本（或更早版本）的 Azure AD Connect 服务器。 有关此问题的详细信息，请参阅文章[自动升级后 Azure AD Connect 无法正常工作](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)。
* 修复了遇到错误后导致每 5 分钟重试自动升级的问题。 通过此次修复，遇到错误时，自动升级重试次数将指数式下降。
* 修复了密码同步事件 611 在 Windows 应用程序事件日志中错误显示（显示为“信息”而不是“错误”）的问题********。 只要密码同步出现问题，便会生成事件 611。 
* 修复了 Azure AD Connect 向导中允许在未选择组写回所需的 OU 的情况下启用组写回功能的问题。

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 在 Azure AD Connect 向导中的其他任务下添加了故障排除任务。 客户可以利用此任务解决与密码同步相关的问题并收集常规诊断信息。 未来，故障排除任务将扩展到能处理与目录同步相关的其他问题。
* Azure AD Connect 现在支持名为“使用现有数据库”的新安装模式****。 在此安装模式下，客户可以安装指定现有 ADSync 数据库的 Azure AD Connect。 有关此功能的详细信息，请参阅文章[使用现有数据库](how-to-connect-install-existing-database.md)。
* 为了提高安全性，Azure AD Connect 现在默认为使用 TLS1.2 连接到 Azure AD 进行目录同步。 以前默认使用 TLS1.0。
* Azure AD Connect 密码同步代理启动时，它将尝试连接到 Azure AD 的已知终结点进行密码同步。 成功连接后，它会重定向到特定于区域的终结点。 以前，除非重新启动，否则密码同步代理将一直缓存区域特定的终结点。 现在，当遇到特定于区域的终结点的连接问题时，代理将清除缓存并重试连接已知终结点。 此更改可确保已缓存的区域特定的终结点不再可用时，密码同步可以故障转移到其他区域特定的终结点。
* 若要同步本地 AD 林中的更改，需要 AD DS 帐户。 可以 (i) 自行创建 AD DS 帐户，并将其凭据提供给 Azure AD Connect，也可以 (ii) 提供企业管理员凭据，让 Azure AD Connect 为你创建 AD DS 帐户。 以前，(i) 是 Azure AD Connect 向导中的默认选项。 现在，(ii) 是默认选项。

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 添加了对 Microsoft Azure 政府云和 Microsoft 云德国的支持。

### <a name="ad-fs-management"></a>AD FS 管理
#### <a name="fixed-issues"></a>修复的问题
* AD 准备 PowerShell 模块中的 ADSyncNGCKeysWriteBack cmdlet 错误地将 Acl 应用到设备注册容器，因此仅继承现有权限。  已对此情况进行更新，以便同步服务帐户具有正确的权限。

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 已更新 AAD Connect 验证 ADFS 登录任务，以便它能验证针对 Microsoft Online 的登录名而不只是验证从 ADFS 检索到的令牌。
* 使用 AAD Connect 设置新 ADFS 场时，请求 ADFS 凭据的页面已经移动，现在此页面在要求用户提供 ADFS 和 WAP 服务器之前出现。  通过此更改，AAD Connect 可以检查指定帐户是否具有正确的权限。
* AAD Connect 升级期间，如果 ADFS AAD 信任无法更新，升级将不会失败。  如果发生此情况，用户将看到相应警告消息，并应通过其他 AAD Connect 任务继续重置信任。

### <a name="seamless-single-sign-on"></a>无缝单一登录
#### <a name="fixed-issues"></a>修复的问题
* 修复了尝试启用[无缝单一登录](how-to-connect-sso.md)导致 Azure AD Connect 向导返回错误的问题。 错误消息为“Microsoft Azure AD Connect 身份验证代理的配置失败”**。 此问题会影响以下现有用户：基于此[文章](how-to-connect-pta-upgrade-preview-authentication-agents.md)介绍的步骤，手动升级预览版身份验证代理以进行[传递身份验证](how-to-connect-sso.md)的客户。


## <a name="115610"></a>1.1.561.0
状态：2017 年 7 月 23 日

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>修复的问题

* 修复了导致现成的同步规则“Out to AD - User ImmutableId”被删除的一个问题：

  * 当升级 Azure AD Connect 时，或使用 Azure AD Connect 向导中的任务选项“更新同步配置”来更新 Azure AD Connect 同步配置时，会出现此问题。**
  
  * 此同步规则适用于启用了[将 ms-DS-ConsistencyGuid 用作源定位点](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)功能的客户。 版本 1.1.524.0 及更高版本中引入了此功能。 当删除此同步规则后，Azure AD Connect 无法再使用 ObjectGuid 属性值填充本地 AD ms-DS-ConsistencyGuid 属性。 它不会阻止将新用户预配到 Azure AD 中。
  
  * 此修复可以确保在启用了该功能的情况下，在升级期间或者在更改配置期间不再会删除此功能。 对于已受此问题影响的现有客户，此修复还可以确保在升级到此版本的 Azure AD Connect 之后将同步规则添加回来。

* 修复了一个导致现成的同步规则的优先级值小于 100 的问题：

  * 通常，优先级值 0 - 99 是为自定义同步规则保留的。 在升级期间，现成的同步规则的优先级值进行了更新以适应同步规则更改。 由于此问题，可能会为现成的同步规则分配一个小于 100 的优先级值。
  
  * 此修复可以防止升级期间发生此问题。 不过，对于已受此问题影响的现有客户，它不会还原优先级值。 将来会提供一个单独的修复来帮助进行还原。

* 修复了即使在启用了基于 OU 的筛选的情况下，Azure AD Connect 向导中的“[域和 OU 筛选](how-to-connect-install-custom.md#domain-and-ou-filtering)”屏幕也将“同步所有域和 OU”选项显示为已选中的问题。**

*   修复了一个当单击“刷新”按钮时导致 Synchronization Service Manager 中的“[配置目录分区](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)”屏幕返回错误的问题。** 错误消息为“刷新域时遇到错误: 无法将‘System.Collections.ArrayList’类型的对象转换为‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject’类型。** 当已将新的 AD 域添加到现有的 AD 林并尝试使用“刷新”按钮更新 Azure AD Connect 时会出现此错误。

#### <a name="new-features-and-improvements"></a>新增功能和改进

* [自动升级功能](how-to-connect-install-automatic-upgrade.md)已扩展为支持具有以下配置的客户：
  * 已启用了设备写回功能。
  * 已启用了组写回功能。
  * 安装不是快速设置或 DirSync 升级。
  * metaverse 中的对象超过 100,000 个。
  * 正在连接到多个林。 快速设置仅连接到一个林。
  * AD 连接器帐户不再是默认的 MSOL_ 帐户。
  * 服务器已设置为过渡模式。
  * 已启用了用户写回功能。
  
  >[!NOTE]
  >自动升级功能的范围扩展影响采用 Azure AD Connect 内部版本 1.1.105.0 及更高版本的客户。 如果不希望 Azure AD Connect 服务器自动升级，必须在 Azure AD Connect 服务器上运行以下 cmdlet：`Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`。 有关启用/禁用自动升级的详细信息，请参阅 [Azure AD Connect：自动升级](how-to-connect-install-automatic-upgrade.md)一文。

## <a name="115580"></a>1.1.558.0
状态：不会发行。 版本 1.1.561.0 中包括了此内部版本中的更改。

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>修复的问题

* 修复了当更新基于 OU 的筛选配置时导致现成的同步规则“Out to AD - User ImmutableId”被删除的一个问题。 此同步规则是[将 ms-DS-ConsistencyGuid 用作源定位点](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)功能所必需的。

* 修复了即使在启用了基于 OU 的筛选的情况下，Azure AD Connect 向导中的“[域和 OU 筛选](how-to-connect-install-custom.md#domain-and-ou-filtering)”屏幕也将“同步所有域和 OU”选项显示为已选中的问题。**

*   修复了一个当单击“刷新”按钮时导致 Synchronization Service Manager 中的“[配置目录分区](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)”屏幕返回错误的问题。** 错误消息为“刷新域时遇到错误: 无法将‘System.Collections.ArrayList’类型的对象转换为‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject’类型。** 当已将新的 AD 域添加到现有的 AD 林并尝试使用“刷新”按钮更新 Azure AD Connect 时会出现此错误。

#### <a name="new-features-and-improvements"></a>新增功能和改进

* [自动升级功能](how-to-connect-install-automatic-upgrade.md)已扩展为支持具有以下配置的客户：
  * 已启用了设备写回功能。
  * 已启用了组写回功能。
  * 安装不是快速设置或 DirSync 升级。
  * metaverse 中的对象超过 100,000 个。
  * 正在连接到多个林。 快速设置仅连接到一个林。
  * AD 连接器帐户不再是默认的 MSOL_ 帐户。
  * 服务器已设置为过渡模式。
  * 已启用了用户写回功能。
  
  >[!NOTE]
  >自动升级功能的范围扩展影响采用 Azure AD Connect 内部版本 1.1.105.0 及更高版本的客户。 如果不希望 Azure AD Connect 服务器自动升级，必须在 Azure AD Connect 服务器上运行以下 cmdlet：`Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`。 有关启用/禁用自动升级的详细信息，请参阅 [Azure AD Connect：自动升级](how-to-connect-install-automatic-upgrade.md)一文。

## <a name="115570"></a>1.1.557.0
状态：2017 年 7 月

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>修复的问题
* 修复了 Initialize-ADSyncDomainJoinedComputerSync cmdlet 中导致现有服务连接点对象上配置的仍然有效的已验证域更改的问题。 当 Azure AD 租户具有可用于配置服务连接点的多个已验证域时会发生此问题。

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 密码写回现在可供 Microsoft Azure 政府版云和德国 Microsoft 云的预览版使用。 有关 Azure AD Connect 对各种服务实例的支持的详细信息，请参阅 [Azure AD Connect：实例的特殊注意事项](reference-connect-instances.md)。

* Initialize-ADSyncDomainJoinedComputerSync cmdlet 现在有一个新的名为 AzureADDomain 的可选参数。 此参数允许你指定要用于配置服务连接点的已验证域。

### <a name="pass-through-authentication"></a>直通身份验证

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 直通身份验证所需代理的名称已从 *Microsoft Azure AD 应用程序代理连接器*更改为 *Microsoft Azure AD Connect 身份验证代理*。

* 启用直通身份验证不再会默认启用密码哈希同步。


## <a name="115530"></a>1.1.553.0
状态：2017 年 6 月

> [!IMPORTANT]
> 此版本引入了架构和同步规则更改。 Azure AD Connect 同步服务在升级后将触发完全导入和完全同步步骤。 下面介绍了更改详细信息。 若要在升级后临时推迟完全导入和完全同步步骤，请参阅[如何在升级后推迟完全同步](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)一文。
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>已知问题
* 存在一个问题，该问题会影响将[基于 OU 的筛选](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)与 Azure AD Connect 同步配合使用的客户。导航到 "Azure AD Connect" 向导中的 "[域和 OU 筛选" 页](how-to-connect-install-custom.md#domain-and-ou-filtering)时，应出现下列行为：
  * 如果启用了基于 OU 的筛选，则会选中“同步选定的域和 OU”选项。****
  * 否则会选中“同步所有域和 OU”选项。****

发生的问题是在运行向导时始终会选中“同步所有域和 OU”选项。****  即使以前配置了基于 OU 的筛选也会发生此问题。 在保存任何 AAD Connect 配置更改之前，请确保选中了“同步选定的域和 OU”选项并确认需要同步的所有 OU 已再次启用。**** 否则将会禁用基于 OU 的筛选。

#### <a name="fixed-issues"></a>修复的问题

* 修复了密码写回功能的一个问题，该问题允许 Azure AD 管理员重置本地 AD 特权用户帐户的密码。 当向 Azure AD Connect 授予了对特权帐户的“重置密码”权限时会发生此问题。 此版本的 Azure AD Connect 通过以下方法解决了此问题：不允许 Azure AD 管理员重置任意本地 AD 特权用户帐户的密码，除非管理员是该帐户的所有者。 有关详细信息，请参阅[安全公告 4033453](https://technet.microsoft.com/library/security/4033453)。

* 修复了与[将 ms-DS-ConsistencyGuid 用作源定位点](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能相关的一个问题，该问题导致 Azure AD Connect 不写回到本地 AD ms-DS-ConsistencyGuid 属性。 当向 Azure AD Connect 中添加了多个本地 AD 林并选中了“用户标识跨多个目录存在”选项时会发生此问题。** 使用这样的配置时，得到的同步规则不会填充 Metaverse 中的 sourceAnchorBinary 属性。 sourceAnchorBinary 属性用作 ms-DS-ConsistencyGuid 属性的源属性。 因此，没有发生到 ms-DSConsistencyGuid 属性的写回。 为了修复此问题，已更新了以下同步规则来确保始终填充 Metaverse 中的 sourceAnchorBinary 属性：
  * In from AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* 以前，即使未启用[将 ms-DS-ConsistencyGuid 用作源定位点](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能，也会将“Out to AD – User ImmutableId”同步规则添加到 Azure AD Connect。 这具有积极影响，且不会导致出现 ms-DS-ConsistencyGuid 属性写回情况。 为避免混淆，已添加了逻辑来确保只有启用了此功能时才添加同步规则。

* 修复了一个导致密码哈希同步失败且出现错误事件 611 的问题。 从本地 AD 中删除一个或多个域控制器后会发生此问题。 在每个密码同步周期结束时，本地 AD 发出的同步 cookie 包含 USN（更新序号）值为 0 的已删除域控制器的调用 ID。 密码同步管理器无法持久保存包含 USN 值 0 的同步 cookie，并且会失败且出现错误事件 611。 在下一个同步周期中，密码同步管理器将重新使用不包含 USN 值 0 的最后一个已持久保存的 cookie。 这将导致重新同步相同的密码更改。 使用此修复，密码同步管理器可以正确地持久保存同步 cookie。

* 以前，即使已使用 Set-ADSyncAutoUpgrade cmdlet 禁用了自动升级，自动升级过程也会定期检查升级，并依赖于已下载的安装程序来遵守停用。 使用此修复，自动升级过程不再定期检查升级。 在执行一次此 Azure AD Connect 版本的升级安装程序后会自动应用此修复。

#### <a name="new-features-and-improvements"></a>新增功能和改进

* 以前，[将 ms-DS-ConsistencyGuid 用作源定位点](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能仅可用于新部署。 现在，它可用于现有部署。 更具体地说：
  * 若要访问此功能，请启动 Azure AD Connect 向导并选择“更新源锚点”选项。**
  * 只会针对将 objectGuid 用作源锚点属性的现有部署显示此选项。
  * 配置此选项时，向导会验证本地 Active Directory 目录中 ms-DS-ConsistencyGuid 属性的状态。 如果未在目录中的任何用户对象上配置此属性，则向导会将 ms-DS-ConsistencyGuid 用作 sourceAnchor 属性。 如果已在目录中的一个或多个用户对象上配置了该属性，向导就会认为该属性正由其他应用程序使用，不适合用作 sourceAnchor 属性，并且不允许执行源锚点更改。 如果确定该属性未由现有应用程序使用，则需联系支持部门，了解如何取消显示该错误。

* 特定于设备对象上的 **userCertificate** 属性，Azure AD Connect 现在会查找[将已加入域的设备连接到 Azure AD 以获得 Windows 10 体验](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)所需的证书值，并且会在同步到 Azure AD 之前将其余的筛选掉。 为了启用此行为，已更新了现成的同步规则“Out to AAD - Device Join SOAInAD”。

* Azure AD Connect 现在支持将 Exchange Online **cloudPublicDelegates** 属性写回到本地 AD **publicDelegates** 属性。 这允许向拥有本地 Exchange 邮箱的用户授予对 Exchange Online 邮箱的 SendOnBehalfTo 权限。 为了支持此功能，已添加了新的现成的同步规则“Out to AD – User Exchange Hybrid PublicDelegates writeback”。 只有当启用了 Exchange 混合功能时才会将此同步规则添加到 Azure AD Connect。

* Azure AD Connect 现在支持从 Azure AD 同步 **altRecipient** 属性。 为了支持此更改，已更新了以下现成的同步规则来包括所需的属性流：
  * In from AD – User Exchange
  * Out to AAD – User ExchangeOnline
  
* Metaverse 中的 **cloudSOAExchMailbox** 属性指示给定的用户是否具有 Exchange Online 邮箱。 已更新了其定义来包括额外的 Exchange Online RecipientDisplayTypes，例如“设备”邮箱和“会议室”邮箱。 为启用此更改，cloudSOAExchMailbox 属性（位于现成的同步规则“In from AAD – User Exchange Hybrid”下）的定义已从

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... 更改为以下内容：

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* 添加了用于创建同步规则表达式的以下 X509Certificate2 兼容函数集来处理 userCertificate 属性中的证书值：

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Select|
    |CertKeyAlgorithmParams|CertHashString|其中|
    |||With|

* 引入了以下架构更改来允许客户为流 sAMAccountName、domainNetBios 和组对象的 domainFQDN 以及用户对象的 distinguishedName 创建自定义同步规则：

  * 向 MV 架构添加了以下属性：
    * Group: AccountName
    * Group: domainNetBios
    * Group: domainFQDN
    * Person: distinguishedName

  * 向 Azure AD Connector 架构添加了以下属性：
    * Group: OnPremisesSamAccountName
    * Group: NetBiosName
    * Group: DnsDomainName
    * User: OnPremisesDistinguishedName

* ADSyncDomainJoinedComputerSync cmdlet 脚本现在有一个新的名为 AzureEnvironment 的可选参数。 此参数用来指定对应的 Azure Active Directory 租户托管在哪个区域中。 有效值包括：
  * AzureCloud（默认值）
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* 已更新了同步规则编辑器来将“加入”（而非“预配”）用作创建同步规则时链接类型的默认值。

### <a name="ad-fs-management"></a>AD FS 管理

#### <a name="issues-fixed"></a>修复的问题

* 下列 URL 是 Azure AD 引入的新的 WS-联盟终结点，用于改进针对身份验证故障的恢复能力，并将添加到本地 AD FS 回复方信任配置：
  * https： \/ /ests.login.microsoftonline.com/login.srf
  * https： \/ /stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* 修复了一个导致 AD FS 为 IssuerID 生成不正确的声明值的问题。 如果 Azure AD 租户中有多个已验证的域并且用来生成 IssuerID 声明的 userPrincipalName 属性至少有 3 层的深度（例如 johndoe@us.contoso.com），则会发生此问题。 可以通过更新声明规则使用的正则表达式来解决此问题。

#### <a name="new-features-and-improvements"></a>新增功能和改进
* 以前，Azure AD Connect 提供的 ADFS 证书管理功能只能用于通过 Azure AD Connect 管理的 ADFS 场。 现在，可以将此功能用于未使用 Azure AD Connect 管理的 ADFS 场。

## <a name="115240"></a>1.1.524.0
发布日期：2017 年 5 月

> [!IMPORTANT]
> 此版本引入了架构和同步规则更改。 Azure AD Connect 同步服务在升级后将触发完全导入和完全同步步骤。 下面介绍了更改详细信息。
>
>

**已修复的问题：**

Azure AD Connect 同步

* 修复了导致自动升级的问题，即使客户已禁用使用 Set-ADSyncAutoUpgrade cmdlet 的功能在 Azure AD Connect 服务器上发生。 使用此修复后，服务器上的自动升级进程仍升级会定期检查，但下载安装程序自动升级的配置。
* 在 DirSync 就地升级时，Azure AD Connect 创建由 Azure AD 连接器用于与 Azure AD 同步的 Azure AD 服务帐户。 创建帐户后，Azure AD Connect 使用该帐户在 Azure AD 中进行身份验证。 有时，身份验证由于暂时性问题失败，而这又会导致 DirSync 就地升级失败，并出现错误“执行配置 AAD 同步任务时出错: AADSTS50034: 要登录到此应用程序，必须将帐户添加到 xxx.onmicrosoft.com 目录。”** 若要提高复原力 DirSync 升级，Azure AD Connect 现在重试此身份验证步骤。
* 生成 443 导致 DirSync 的就地升级才能成功的问题，但不是创建所需的目录同步的运行配置文件。 修复逻辑包括在 Azure AD Connect 的此版本中。 在客户升级到此版本中，Azure AD Connect 将检测到缺少运行配置文件，将创建它们。
* 修复了将导致无法启动事件 ID 6900 和错误，密码同步过程的问题“已添加具有相同键的项”**。 如果更新筛选配置以包括 AD 配置分区的 OU，则会发生此问题。 若要解决此问题，密码同步过程现在同步从 AD 域分区的密码更改。 非域分区，例如配置分区将跳过。
* Express 安装期间，Azure AD Connect 创建本地 AD 连接器要使用其可与其 AD DS 帐户本地 AD。 以前，帐户创建具有用户帐户控制属性上设置了 PASSWD_NOTREQD 标志和的帐户上设置的随机密码。 现在，Azure AD Connect 显式删除 PASSWD_NOTREQD 标志后的帐户上设置密码。
* 修复了导致出现错误的 DirSync 升级失败的问题“死锁时，出现了 sql server 中哪些获取的应用程序锁”** mailNickname 属性位于在本地 AD 架构，但不是限定于 AD 用户对象类。
* 修复将导致设备写回功能，当管理员正在更新 Azure AD Connect 同步配置使用 Azure AD Connect 向导自动禁用了问题。 之所以出现此问题是因为，向导对本地 AD 中的现有设备写回配置执行先决条件检查，而检查失败。 解决方法是以跳过检查，如果以前已启用设备写回。
* 若要配置 OU 筛选，可以使用 Azure AD Connect 向导或同步服务管理器。 以前，如果使用 Azure AD Connect 向导配置 OU 筛选，则会包含以后新建的 OU 用于目录同步。 如果不想要包含新 OU，则必须使用同步服务管理器配置 OU 筛选。 现在，可以获得相同的行为使用 Azure AD Connect 向导。
* 修复了会导致 Azure AD Connect 所需下创建的架构的安装的管理，而不是在 dbo 架构下的存储的过程的问题。
* 修复了问题导致省略 AAD 连接服务器事件日志中的 Azure AD 返回的 TrackingId 属性。 如果 Azure AD Connect 会从 Azure AD 收到一个重定向消息，Azure AD Connect 无法连接到提供的终结点，则会发生此问题。 TrackingId 由支持工程师用于在故障排除过程使用服务端日志关联。
* 当 Azure AD Connect 收到 Azure AD 中的 LargeObject 错误时，Azure AD Connect 将生成一个事件，其中包含 EventID 6941 和消息 *"预配的对象太大。剪裁此对象上属性值的数目。 "* 同时，Azure AD Connect 还会生成误导性事件 EventID 6900 和消息“Microsoft.Online.Coexistence.ProvisionRetryException: 无法通信与 Windows Azure Active Directory 服务。”** 为了尽量减少混淆，Azure AD Connect 不再生成后一种事件收到 LargeObject 错误时。
* 修复了当尝试更新通用 LDAP 连接器的配置时，Synchronization Service Manager 无法响应的问题。

**新功能/改进：**

Azure AD Connect 同步
* 同步规则更改 – 实现了以下同步规则更改：
  * 如果属性有 15 个以上的值，更新的默认同步规则设置为不导出属性 **userCertificate** 和 **userSMIMECertificate**。
  * AD 属性 **employeeID** 和 **msExchBypassModerationLink** 现在包含在默认同步规则集中。
  * AD 属性 **photo** 已从默认同步规则集中删除。
  * 添加 **preferredDataLocation** 到 Metaverse 架构和 AAD 连接器架构。 客户想要在 Azure AD 中更新任一属性可以实现自定义同步规则，可以这样做。 
  * 添加 **userType** 到 Metaverse 架构和 AAD 连接器架构。 客户想要在 Azure AD 中更新任一属性可以实现自定义同步规则，可以这样做。

* Azure AD Connect 现在会自动为本地 AD 对象启用将 ConsistencyGuid 属性用作源锚点属性。 而且，如果 ConsistencyGuid 属性为空，则 Azure AD Connect 会使用 objectGuid 属性值填充该属性。 此功能非常适用于新的部署。 要了解有关此功能的详细信息，请参阅 [Azure AD Connect：设计概念 - 将 ms-DS-ConsistencyGuid 用作 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 部分。
* 新故障排除 cmdlet Invoke-ADSyncDiagnostics 已添加以帮助诊断密码哈希同步相关的问题。 有关使用此 cmdlet 的信息，请参阅[使用 Azure AD Connect 同步排查密码哈希同步问题](tshoot-connect-password-hash-synchronization.md)。
* Azure AD Connect 现在支持同步已启用邮件公用文件夹对象从本地 AD 到 Azure AD。 可以启用功能使用在可选功能下的 Azure AD Connect 向导。 若要详细了解此功能，请参阅[对本地已启用邮件的公用文件夹的基于 Office 365 目录的边缘阻止支持](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders)。
* Azure AD Connect 需要一个 AD DS 帐户来从本地 AD 进行同步。 以前，如果使用快捷模式安装了 Azure AD Connect，则可以提供企业管理员帐户的凭据并且 Azure AD Connect 将创建所需的 AD DS 帐户。 但是，对于自定义安装和将林添加到现有部署，则会要求你提供 AD DS 帐户。 现在，在自定义安装过程中你还可以选择提供企业管理员帐户的凭据，并允许创建 AD DS 帐户所需的 Azure AD Connect。
* Azure AD Connect 现在支持 SQL AOA。 安装 Azure AD Connect 之前，必须启用 SQL AOA。 在安装期间，Azure AD Connect 会检测是否已为提供的 SQL 实例启用 SQL AOA。 如果启用了 SQL AOA，Azure AD Connect 进一步指出如果 SQL AOA 配置为使用同步复制或异步复制。 当设置可用性组侦听器，建议将 RegisterAllProvidersIP 属性设置为 0。 之所以提供此建议是因为，Azure AD Connect 当前使用 SQL Native Client 连接到 SQL，并且 SQL Native Client 不支持使用 MultiSubNetFailover 属性。
* 如果将 LocalDB 用作 Azure AD Connect 的数据库并且已达到该数据库的 10-GB 限制，同步服务不再启动。 以前，需要执行 ShrinkDatabase LocalDB 回收足够的数据库空间要启动同步服务操作。 后来，可以使用同步服务管理器删除以回收更多的数据库空间的运行历史记录。 现在，可以使用开始 ADSyncPurgeRunHistory cmdlet 运行的清除历史记录数据从 LocalDB 回收数据库空间。 此外，此 cmdlet 支持脱机模式（通过指定 -offline 参数）的同步服务未运行时就可以使用。 注意：脱机模式下只能如果同步服务未运行，并且使用的数据库是 LocalDB。
* 要减少存储所需的空间，Azure AD Connect 会将同步错误详细信息现在压缩之前将它们存储在 LocalDB/SQL 数据库。 从 Azure AD Connect 的较旧版本升级到此版本，Azure AD Connect 将执行一次性压缩现有同步错误详细信息。
* 以前，在更新后 OU 筛选配置，必须手动运行完全导入以确保现有对象都正确包括/排除从目录同步。 现在，Azure AD Connect 会自动触发完全导入期间下次同步周期。 此外，完整导入是仅应用于受更新 AD 连接器。 注意：此项改进是适用于筛选使用仅在 Azure AD Connect 向导所做的更新的 OU。 不适用于筛选使用 Synchronization Service Manager 所做的更新的 OU。
* 以前，仅基于组的筛选支持用户、组和联系人对象。 现在，基于组的筛选还支持计算机对象。
* 以前，可以删除连接器空间数据，但不能禁用 Azure AD Connect 同步计划程序。 现在，如果它检测到计划程序已启用同步服务管理器将阻止删除连接器空间数据。 此外，则会返回警告，通知客户有关可能数据丢失，如果删除连接器空间数据。
* 以前，必须禁用 Azure AD Connect 向导才能正确运行的 PowerShell 脚本。 此问题已部分解决。 如果使用 Azure AD Connect 向导来管理同步配置，则可以启用 PowerShell 脚本。 如果使用 Azure AD Connect 向导来管理 ADFS 配置，则必须禁用 PowerShell 脚本。



## <a name="114860"></a>1.1.486.0
发布日期：2017 年 4 月

**已解决的问题：**
* 修复了 Azure AD Connect 无法在本地化版本的 Windows Server 上成功安装的问题。

## <a name="114840"></a>1.1.484.0
发布日期：2017 年 4 月

**已知问题：**

* 如果以下条件全部成立，则不能成功安装 Azure AD Connect 的此版本：
   1. 正在执行 DirSync 的就地升级或 Azure AD Connect 的全新安装。
   2. 使用的是本地化版本的 Windows Server，该服务器上的内置 Administrator 组的名称不是“Administrators”。
   3. 使用的是随 Azure AD Connect 一起安装的默认 SQL Server 2012 Express LocalDB，而不是自己的完整 SQL。

**已修复的问题：**

Azure AD Connect 同步
* 修复了当一个或多个连接器缺少某个同步步骤的运行配置文件时，同步计划程序会完全跳过这一同步步骤的问题。 例如，使用 Synchronization Service Manager 手动添加了连接器，但没有为其创建增量导入运行配置文件。 此修补程序可以确保同步计划程序继续运行其他连接器的增量导入。
* 修复了当 Synchronization Service 在运行步骤的其中一步遇到问题时，会立即停止处理运行配置文件的问题。 此修补程序可以确保 Synchronization Service 会跳过该运行步骤，并继续处理其余步骤。 例如，具有多个运行步骤的 AD 连接器的增量导入运行配置文件（每个本地 AD 域一个步骤）。 即使其中一个 AD 域出现网络连接问题，Synchronization Service 也会运行其他 AD 域的增量导入。
* 修复了自动升级期间会跳过 Azure AD 连接器更新的问题。
* 修复了安装过程中 Azure AD 连接器不能正确识别服务器是否是域控制器，进而导致 DirSync 升级失败的问题。
* 修复了 DirSync 就地升级不会为 Azure AD 连接器创建任何运行配置文件的问题。
* 修复了当尝试配置通用的 LDAP 连接器时，Synchronization Service Manager 用户界面无法响应的问题。

AD FS 管理
* 修复了如果 AD FS 主节点已移至其他服务器，Azure AD Connect 向导会失败的问题。

桌面 SSO
* 修复了 Azure AD Connect 向导中的问题，即在新的安装过程中，如果选择“密码同步”作为登录选项，则无法在登录屏幕中启用桌面 SSO 功能。

**新功能/改进：**

Azure AD Connect 同步
* Azure AD Connect Sync 现在支持使用虚拟服务帐户、托管服务帐户和组托管服务帐户作为其服务帐户。 此功能仅适用于新安装的 Azure AD Connect。 安装 Azure AD Connect 时：
    * 默认情况下，Azure AD Connect 向导会创建一个虚拟服务帐户，并将其用作服务帐户。
    * 如果是在域控制器上进行安装，Azure AD Connect 会回退到之前的处理方式，即创建一个域用户帐户，并将其用作服务帐户。
    * 可以通过提供以下帐户之一来替代默认的处理方式：
      * 组托管服务帐户
      * 托管服务帐户
      * 域用户帐户
      * 本地用户帐户
* 之前，如果升级到的 Azure AD Connect 新版本中更新了连接器或更改了同步规则，Azure AD Connect 将触发完全同步周期。 现在，Azure AD Connect 有选择性地仅对进行了更新的连接器触发完全导入步骤，对更改了同步规则的连接器触发完全同步步骤。
* 之前，导出删除阈值仅适用于通过同步计划程序触发的导出。 现在，此功能扩展到了适用于客户使用 Synchronization Service Manager 手动触发导出的情况。
* Azure AD 租户有一个服务配置，该配置指示了是否已为租户启用密码同步功能。 之前，同时拥有活动和暂存服务器时，Azure AD Connect 很容易错误配置服务配置。 现在，Azure AD Connect 会尝试让服务配置只和活动 Azure AD Connect 服务器保持一致。
* 如果本地 AD 未启用 AD 回收站，Azure AD Connect 向导会检测并返回警告。
* 之前，如果批处理的对象总大小超过特定阈值，导出到 Azure AD 会超时并失败。 现在遇到此问题时，Synchronization Service 会再次尝试以较小批次单独重新发送对象。
* 已经将 Synchronization Service Key Management 应用程序从 Windows 开始菜单中删除。 仍然支持使用 miiskmu.exe 通过命令行接口管理加密密钥。 有关加密密钥的详细信息，请参阅文章 [Abandoning the Azure AD Connect Sync encryption key](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key)（弃用 Azure AD Connect 同步加密密钥）。
* 之前，如果更改了 Azure AD Connect 同步服务帐户密码，则无法正常启动 Synchronization Service，除非已弃用加密密钥并重新初始化 Azure AD Connect 同步服务帐户密码。 现在不再需要执行此过程。

桌面 SSO

* 配置传递身份验证和桌面 SSO 时，Azure AD Connect 向导不再需要在网络上打开端口 9090。 只需要端口 443。 

## <a name="114430"></a>1.1.443.0
发布日期：2017 年 3 月

**已修复的问题：**

Azure AD Connect 同步
* 修复了在 Azure AD 连接器的显示名称没有包含分配给 Azure AD 租户的初始 onmicrosoft.com 域时，Azure AD Connect 向导会失败的问题。
* 修复了在同步服务帐户的密码包含特殊字符（如撇号、冒号和空格）的情况下，在与 SQL 数据库进行连接时，Azure AD Connect 向导会失败的问题。
* 修复了在暂时不同步某个本地 AD 对象，再将其进行同步后，暂存模式下的 Azure AD Connect 服务器上将出现“该映像具有不同于映像的定位点”错误的问题。
* 修复了在暂时不同步某个本地 AD 对象，然后再将其进行同步后，暂存模式下的 Azure AD Connect 服务器上将出现“DN 定位的对象是一个幻影”错误的问题。

AD FS 管理
* 修复了在配置备用登录 ID 后，Azure AD Connect 向导不会更新 AD FS 配置并设置对信赖方信任的正确声明的问题。
* 修复了 Azure AD Connect 向导无法正确处理 AD FS 服务器（该服务器的服务帐户是通过 userPrincipalName 格式设置的，而非 sAMAccountName 格式）的问题。

直通身份验证
* 修复了在选择了“直通身份验证”但其连接器注册失败时，Azure AD Connect 向导会失败的问题。
* 修复了在启用了桌面 SSO 功能时，Azure AD Connect 向导将绕过对所选登录方法的验证检查。

密码重置
* 修复了这样一个问题：当连接已由防火墙或代理终止时，该问题可能会导致 Azure AAD Connect 服务器不尝试重新连接。

**新功能/改进：**

Azure AD Connect 同步
* Get-ADSyncScheduler cmdlet 现在可返回一个名为 SyncCycleInProgress 的新的布尔属性。 如果返回的值为 true，则意味着正在进行计划的同步周期。
* 已用于存储 Azure AD Connect 安装和安装程序日志的目标文件夹从 %localappdata%\AADConnect 移至 %programdata%\AADConnect，以提高日志文件的可访问性。

AD FS 管理
* 添加了对更新 AD FS 场 TLS/SSL 证书的支持。
* 添加了对管理 AD FS 2016 的支持。
* 现在可以在 AD FS 安装期间指定现有 gMSA（组托管服务帐户）。
* 现在可以将 SHA-256 配置为 Azure AD 信赖方信任的签名哈希算法。

密码重置
* 引入了多项改进，以允许产品在具有更严格防火墙规则的环境中正常工作。
* 提高了与 Azure 服务总线的连接可靠性。

## <a name="113800"></a>1.1.380.0
发布日期：2016 年 12 月

**已修复的问题：**

* 修复了本版本中缺少针对 Active Directory 联合身份验证服务 (AD FS) 的 issuerid 声明规则的问题。

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

## <a name="113710"></a>1.1.371.0
发布日期：2016 年 12 月

**已知问题：**

* 本版本中缺少针对 AD FS 的 issuerid 声明规则。 要将多个域与 Azure Active Directory (Azure AD) 联合，需使用 issuerid 声明规则。 如果使用 Azure AD Connect 管理本地 AD FS 部署，则升级到此版本将从 AD FS 配置中删除现有 issuerid 声明规则。 可在安装/升级后添加 issuerid 声明规则来解决此问题。 有关添加 issuerid 声明规则的详细信息，请参阅[与 Azure AD 联合的多域支持](how-to-connect-install-multiple-domains.md)一文。

**已修复的问题：**

* 如果未打开用于出站连接的端口 9090，Azure AD Connect 安装或升级会失败。

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

## <a name="113700"></a>1.1.370.0
发布日期：2016 年 12 月

**已知问题：**

* 本版本中缺少针对 AD FS 的 issuerid 声明规则。 要将多个域与 Azure AD 联合，需使用 issuerid 声明规则。 如果使用 Azure AD Connect 管理本地 AD FS 部署，则升级到此版本将从 AD FS 配置中删除现有 issuerid 声明规则。 可在安装/升级后添加 issuerid 声明规则来解决此问题。 有关添加 issuerid 声明规则的详细信息，请参阅[与 Azure AD 联合的多域支持](how-to-connect-install-multiple-domains.md)一文。
* 必须打开用于出站连接的端口 9090 才能完成安装。

**新增功能：**

* 直通身份验证（预览）。

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

## <a name="113430"></a>1.1.343.0
发布日期：2016 年 11 月

**已知问题：**

* 本版本中缺少针对 AD FS 的 issuerid 声明规则。 要将多个域与 Azure AD 联合，需使用 issuerid 声明规则。 如果使用 Azure AD Connect 管理本地 AD FS 部署，则升级到此版本将从 AD FS 配置中删除现有 issuerid 声明规则。 可在安装/升级后添加 issuerid 声明规则来解决此问题。 有关添加 issuerid 声明规则的详细信息，请参阅[与 Azure AD 联合的多域支持](how-to-connect-install-multiple-domains.md)一文。

**已修复的问题：**

* 有时，由于无法创建密码符合组织密码策略指定的复杂性级别的本地服务帐户，安装 Azure AD Connect 失败。
* 解决了当连接器空间中的某个对象既在一个联接规则的范围以外，同时又在另一个联接规则的范围以内时，无法重新评估联接规则的问题。 如果两个或更多个联接规则的联接条件互斥，则可能会发生此问题。
* 解决了当（Azure AD 中）不包含联接规则的入站同步规则的优先级值低于包含联接规则的入站同步规则时，不处理前一种规则的问题。

**措施**

* 添加了在 Windows Server 2016 标准版或更高版本上安装 Azure AD Connect 的支持。
* 添加了将 SQL Server 2016 用作 Azure AD Connect 远程数据库的支持。

## <a name="112810"></a>1.1.281.0
发布日期：2016 年 8 月

**已修复的问题：**

* 只有在下一个同步周期完成后，才对同步间隔进行更改。
* Azure AD Connect 向导不接受用户名开头为下划线 (\_) 的 Azure AD 帐户。
* 如果帐户密码包含太多特殊字符，Azure AD Connect 向导无法对提供的 Azure AD 帐户进行身份验证。 此时会返回错误消息“无法验证凭据。 发生意外错误” 将返回。
* 卸载暂存服务器会在 Azure AD 租户中禁用密码同步，导致活动服务器的密码同步失败。
* 在用户未存储密码哈希的罕见情况下，密码同步失败。
* 当 Azure AD Connect 服务器启用暂存模式时，不会暂时禁用密码写回。
* 当服务器处于暂存模式时，Azure AD Connect 向导不会显示实际的密码同步和密码写回配置， 而始终将这些配置显示为已禁用。
* 当服务器处于暂存模式时，Azure AD Connect 向导不会保存密码同步和密码写回的配置更改。

**措施**

* 已更新 Start-ADSyncSyncCycle cmdlet，指出是否能够成功启动新的同步周期。
* 已添加 Stop-ADSyncSyncCycle cmdlet，终止当前正在进行的同步周期和操作。
* 已更新 Stop-ADSyncScheduler cmdlet，终止当前正在进行的同步周期和操作。
* 在 Azure AD Connect 向导中配置[目录扩展](how-to-connect-sync-feature-directory-extensions.md)时，现在可选择“Teletex 字符串”类型的 Azure AD 属性。

## <a name="111890"></a>1.1.189.0
发布日期：2016 年 6 月

**已解决的问题和改进：**

* Azure AD Connect 现在可以安装于符合 FIPS 的服务器上。
  * 有关密码同步，请参阅[密码哈希同步和 FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips)。
* 已修复下列问题：NetBIOS 名称无法解析为 Active Directory 连接器中的 FQDN。

## <a name="111800"></a>1.1.180.0
发布日期：2016 年 5 月

**新增功能：**

* 警告并帮助你验证域（如果运行 Azure AD Connect 之前未执行此操作）。
* 添加了对[德国 Microsoft 云](reference-connect-instances.md#microsoft-cloud-germany)的支持。
* 添加了对最新 [Microsoft Azure 政府云](reference-connect-instances.md#microsoft-azure-government)基础结构的支持，以及新的 URL 要求。

**已解决的问题和改进：**

* 在同步规则编辑器中添加了筛选功能以方便查找同步规则。
* 改进了删除连接器空间时的性能。
* 修复了在同一个运行轮次中同时删除和添加（称为删除/添加）同一个对象时出现的问题。
* 在升级或刷新目录架构时，已禁用的同步规则不再重新启用包含的对象和属性。

## <a name="111300"></a>1.1.130.0
发布日期：2016 年 4 月

**新增功能：**

* 添加了对[目录扩展](how-to-connect-sync-feature-directory-extensions.md)的多值属性的支持。
* 添加了将[自动升级](how-to-connect-install-automatic-upgrade.md)的更多配置变体视为符合升级要求的支持。
* 为[自定义计划程序](how-to-connect-sync-feature-scheduler.md#custom-scheduler)添加了一些 cmdlet。

## <a name="111190"></a>1.1.119.0
发布日期：2016 年 3 月

**已修复的问题：**

* 确定 Windows Server 2008（R2 之前的版本）上无法使用快速安装，因为此操作系统不支持密码同步。
* 使用自定义筛选器配置从 DirSync 升级无法按预期进行。
* 升级到较新版本且没有进行任何配置更改时，不应计划完全导入/同步。

## <a name="111100"></a>1.1.110.0
发布日期：2016 年 2 月

**已修复的问题：**

* 如果安装不位于默认的 C:\Program Files 文件夹中，则无法从旧版升级。
* 如果进行安装，并在安装向导结束时清除“启动同步过程”，再次运行安装向导将不启用计划程序。****
* 在日期/时间格式并非美国英语的服务器上，计划程序将无法正常运行。 此外，还会阻止 `Get-ADSyncScheduler` 返回正确的时间。
* 如果以 AD FS 作为登录选项和升级来安装旧版 Azure AD Connect，便无法再次运行安装向导。

## <a name="111050"></a>1.1.105.0
发布日期：2016 年 2 月

**新增功能：**

* 适用于快速设置客户的[自动升级](how-to-connect-install-automatic-upgrade.md)功能。
* 使用安装向导中的 Azure 多重身份验证和 Privileged Identity Management 来提供全局管理员支持。
  * 如果使用多重身份验证，则代理也需要允许发往 https://secure.aadcdn.microsoftonline-p.com 的流量。
  * 需要将 https://secure.aadcdn.microsoftonline-p.com 添加到受信任的站点列表，这样多重身份验证才能正常工作。
* 允许在初始安装之后更改用户的登录方法。
* 允许在安装向导中使用[域和 OU 筛选](how-to-connect-install-custom.md#domain-and-ou-filtering)。 这也允许连接到并非所有域都可供使用的林。
* [计划程序](how-to-connect-sync-feature-scheduler.md)是同步引擎的内置功能。

**从预览版升级到 GA 的功能：**

* [设备写回](how-to-connect-device-writeback.md)。
* [目录扩展](how-to-connect-sync-feature-directory-extensions.md)。

**新的预览功能：**

* 新的默认同步周期间隔为 30 分钟。 过去所有旧版本都是 3 小时。 添加了对更改[计划程序](how-to-connect-sync-feature-scheduler.md)行为的支持。

**已修复的问题：**

* 验证 DNS 域页面不一定都能识别域。
* 配置 AD FS 时出现域管理员凭据提示。
* 当本地 AD 帐户所在域的 DNS 树与根域不同时，安装向导将无法识别这些帐户。

## <a name="1091310"></a>1.0.9131.0
发布日期：2015 年 12 月

**已修复的问题：**

* 更改 Active Directory 域服务 (AD DS) 中的密码时，密码同步可能不会正常工作，但设置密码时可以正常工作。
* 如果设置了代理服务器，在安装期间或者在配置页上取消升级时，向 Azure AD 进行身份验证可能会失败。
* 如果不是 SQL Server 系统管理员 (SA)，从装有完整 SQL Server 实例的旧版 Azure AD Connect 更新会失败。
* 从装有远程 SQL Server 的旧版 Azure AD Connect 更新时，会显示错误消息“无法访问 ADSync SQL 数据库”。

## <a name="1091250"></a>1.0.9125.0
发布日期：2015 年 11 月

**新增功能：**

* 可将 AD FS 重新配置为 Azure AD 信任。
* 可以刷新 Active Directory 架构和重新生成同步规则。
* 可以禁用同步规则。
* 可将“AuthoritativeNull”定义为同步规则中的新文本。

**新的预览功能：**

* [用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)。
* 支持 [Azure AD 域服务](../user-help/active-directory-passwords-update-your-own-password.md)密码同步。

**新的受支持方案：**

* 支持多个本地 Exchange 组织。 有关详细信息，请参阅[包含多个 Active Directory 林的混合部署](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))。

**已修复的问题：**

* 密码同步问题：
  * 从范围外移到范围内的对象不会同步其密码。 这包括 OU 和属性筛选。
  * 选择要包含在同步中的新 OU 时不需要完全密码同步。
  * 启用已禁用的用户时密码不会同步。
  * 密码重试队列是无限的，以前实施的 5000 个对象限制已停用且已被删除。
* 无法连接到具有 Windows Server 2016 林功能级别的 Active Directory。
* 初始安装后，无法更改用于组筛选的组。
* 对于在启用密码写回的情况下执行密码更改的每个用户，不再能够在 Azure AD Connect 服务器上创建新的用户配置文件。
* 无法在同步规则范围内使用长整数值。
* 如果有无法访问的域控制器，“设备写回”复选框将保持禁用状态。

## <a name="1086670"></a>1.0.8667.0
发布日期：2015 年 8 月

**新增功能：**

* Azure AD Connect 安装向导现已本地化为所有 Windows Server 语言。
* 添加了在使用 Azure AD 密码管理时的帐户解锁支持。

**已修复的问题：**

* 如果另一位用户而不是第一位启动安装的人继续安装，则 Azure AD Connect 安装向导会崩溃。
* 如果 Azure AD Connect 的先前卸载操作无法将 Azure AD Connect Sync 完全卸载，则无法重新安装。
* 如果用户不在林的根域中或使用了非英文版 Active Directory，则无法使用快速安装选项安装 Azure AD Connect。
* 如果无法解析 Active Directory 用户帐户的 FQDN，则会显示“无法提交架构”的误导性错误消息。
* 如果 Active Directory 连接器上使用的帐户已在向导外部更改，向导在进行后续操作时会失败。
* Azure AD Connect 有时无法在域控制器上安装。
* 如果添加了扩展属性，则无法启用和禁用“暂存模式”。
* 由于 Active Directory 连接器上的密码不正确，某些配置中的密码写回失败。
* 如果属性筛选中使用可分辨名称 (DN)，则无法升级 DirSync。
* 使用密码重置时 CPU 使用率过高。

**删除的预览功能：**

* 根据预览版客户的反馈，已暂时删除 [用户写回](how-to-connect-preview.md#user-writeback) 预览版功能。 今后在解决所提供的反馈意见后，我们将再次添加此功能。

## <a name="1086410"></a>1.0.8641.0
发布日期：2015 年 6 月

**Azure AD Connect 的初始版本。**

名称从 Azure AD Sync 更改为 Azure AD Connect。

**新增功能：**

* [快速设置](how-to-connect-install-express.md)安装
* 可以[配置 AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* 可[从 DirSync 升级](how-to-dirsync-upgrade-get-started.md)
* [防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* 引入了[暂存模式](how-to-connect-sync-staging-server.md)

**新的预览功能：**

* [用户写回](how-to-connect-preview.md#user-writeback)
* [组写回](how-to-connect-preview.md#group-writeback)
* [设备写回](how-to-connect-device-writeback.md)
* [目录扩展](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
发布日期：2015 年 5 月

**新要求：**

* Azure AD Sync 现在要求安装 .NET framework 版本 4.5.1。

**已修复的问题：**

* 从 Azure AD 进行密码写回失败并出现 Azure 服务总线连接错误。

## <a name="104910413"></a>1.0.491.0413
发布日期：2015 年 4 月

**已解决的问题和改进：**

* 如果启用回收站且林中存在多个域，Active Directory 连接器不会正确处理删除。
* 对 Azure Active Directory 连接器的导入操作性能有所改进。
* 当某个组超过成员资格限制（默认情况下，此限制设置为 50,000 个对象）时，便会在 Azure Active Directory 中删除该组。 新行为是不删除该组、引发错误且不导出新的成员身份更改。
* 如果连接器空间中已经存在 DN 相同的暂存删除，则无法设置新对象。
* 某些对象需在增量同步期间同步，即使对象上未暂存更改。
* 强制密码同步还会删除首选的 DC 列表。
* CSExportAnalyzer 的某些对象状态存在问题。

**新增功能：**

* 联接现在可以连接到 MV 中的“任何”对象类型。

## <a name="104850222"></a>1.0.485.0222
发布日期：2015 年 2 月

**措施**

* 改进了导入性能。

**已修复的问题：**

* 密码同步具有属性筛选所用的 cloudFiltered 属性。 已筛选的对象不再在密码同步范围中。
* 在拓扑中有大量域控制器的极少数情况下，密码同步不起作用。
* 在设备管理后从 Azure AD 连接器导入时，已在 Azure AD/Intune 中启用了“Stopped-server”。
* 从同一林中的多个域联接外部安全主体 (FSP) 会导致模糊联接错误。

## <a name="104751202"></a>1.0.475.1202
发布日期：2014 年 12 月

**新增功能：**

* 现在支持使用基于属性的筛选执行密码同步。 有关详细信息，请参阅[使用筛选进行密码同步](how-to-connect-sync-configure-filtering.md)。
* ms-DS-ExternalDirectoryObjectID 属性将写回 Active Directory。 此功能添加了对 Office 365 应用程序的支持。 它使用 OAuth2 访问混合 Exchange 部署中的联机邮箱和本地邮箱。

**修复了升级问题：**

* 服务器上提供了登录助手的更新版本。
* 自定义安装路径用于安装 Azure AD Sync。
* 无效的自定义加入条件阻止了升级。

**其他修复：**

* 修复了 Office Pro Plus 的模板。
* 修复了以短划线开头的用户名导致的安装问题。
* 修复了第二次运行安装向导时丢失 sourceAnchor 设置的问题。
* 修复了用于密码同步的 ETW 跟踪。

## <a name="104701023"></a>1.0.470.1023
发布日期：2014 年 10 月

**新增功能：**

* 从多个本地 Active Directory 到 Azure AD 的密码同步。
* 已将安装 UI 本地化为所有的 Windows Server 语言。

**从 AADSync 1.0 GA 升级**

如果已安装 Azure AD Sync，则还必须执行另外一个步骤（考虑到可能已更改现成的同步规则）。 在升级到 1.0.470.1023 版之后，已修改的同步规则会被复制。 对于每个已修改的同步规则，请执行以下操作：

1. 找到已修改的同步规则，并记下所做的更改。
1. 删除同步规则。
1. 找到由 Azure AD Sync 创建的新同步规则，并重新应用所做的更改。

**Active Directory 帐户的权限**

必须为 Active Directory 帐户授予其他权限，才能从 Active Directory 读取密码哈希。 要授予的权限称为“复制目录更改”和“复制目录更改所有项”。 需要这两个权限才能读取密码哈希。

## <a name="104190911"></a>1.0.419.0911
发布日期：2014 年 9 月

**Azure AD Sync 的初始版本。**

## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。

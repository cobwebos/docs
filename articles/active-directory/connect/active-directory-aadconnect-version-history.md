---
title: "Azure AD Connect：版本发布历史记录 | Microsoft 文档"
description: "本文列出 Azure AD Connect 和 Azure AD Sync 的所有版本"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 4703cd03db398d8dc59fb5f5c0cf71214c606bc8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect：版本发布历史记录
Azure Active Directory (Azure AD) 团队会定期更新 Azure AD Sync 的新特性和功能。 并非所有的新增内容都适用于所有受众。

本文旨在帮助你跟踪已发布的版本，并了解你是否需要更新为最新版本。

下面是相关主题的列表：


主题 |  详细信息
--------- | --------- |
从 Azure AD Connect 升级的步骤 | [从旧版升级到最新版](active-directory-aadconnect-upgrade-previous-version.md) Azure AD Connect 的不同方法。
所需的权限 | 有关应用更新时所需的权限，请参阅[帐户和权限](./active-directory-aadconnect-accounts-permissions.md#upgrade)。
下载| [下载 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="115240"></a>1.1.524.0
发布日期：2017 年 5 月

> [!IMPORTANT]
> 此版本引入了架构和同步规则更改。 Azure AD Connect 同步服务在升级后将触发完全导入和完全同步步骤。 下面介绍了更改详细信息。
>
>

**已解决的问题：**

Azure AD Connect 同步

* 修复了导致自动升级的问题，即使客户已禁用使用 Set-ADSyncAutoUpgrade cmdlet 的功能在 Azure AD Connect 服务器上发生。 使用此修复后，服务器上的自动升级进程仍升级会定期检查，但下载安装程序自动升级的配置。
* 在 DirSync 就地升级时，Azure AD Connect 创建由 Azure AD 连接器用于与 Azure AD 同步的 Azure AD 服务帐户。 创建帐户后，Azure AD Connect 使用该帐户在 Azure AD 中进行身份验证。 有时，身份验证由于暂时性问题失败，而这又会导致 DirSync 就地升级失败，并出现错误“执行配置 AAD 同步任务时出错: AADSTS50034: 若要登录到此应用程序，必须将帐户添加到 xxx.onmicrosoft.com 目录。” 若要提高复原力 DirSync 升级，Azure AD Connect 现在重试此身份验证步骤。
* 生成 443 导致 DirSync 的就地升级才能成功的问题，但不是创建所需的目录同步的运行配置文件。 修复逻辑包括在 Azure AD Connect 的此版本中。 在客户升级到此版本中，Azure AD Connect 将检测到缺少运行配置文件，将创建它们。
* 修复了将导致无法启动事件 ID 6900 和错误，密码同步过程的问题“已添加具有相同键的项”。 如果更新筛选配置以包括 AD 配置分区的 OU，则会发生此问题。 若要解决此问题，密码同步过程现在同步从 AD 域分区的密码更改。 非域分区，例如配置分区将跳过。
* Express 安装期间，Azure AD Connect 创建本地 AD 连接器要使用其可与其 AD DS 帐户本地 AD。 以前，帐户创建具有用户帐户控制属性上设置了 PASSWD_NOTREQD 标志和的帐户上设置的随机密码。 现在，Azure AD Connect 显式删除 PASSWD_NOTREQD 标志后的帐户上设置密码。
* 修复了导致出现错误的 DirSync 升级失败的问题“死锁时，出现了 sql server 中哪些获取的应用程序锁”mailNickname 属性位于在本地 AD 架构，但不是限定于 AD 用户对象类。
* 修复将导致设备写回功能，当管理员正在更新 Azure AD Connect 同步配置使用 Azure AD Connect 向导自动禁用了问题。 这被引起向导执行先决条件检查中的现有设备写回配置本地 AD，并检查失败。 解决方法是以跳过检查，如果以前已启用设备写回。
* 若要配置 OU 筛选，可以使用 Azure AD Connect 向导或同步服务管理器。 以前，如果使用 Azure AD Connect 向导配置 OU 筛选，则会包含以后新建的 OU 用于目录同步。 如果不想要包含新 OU，则必须使用同步服务管理器配置 OU 筛选。 现在，可以获得相同的行为使用 Azure AD Connect 向导。
* 修复了会导致 Azure AD Connect 所需下创建的架构的安装的管理，而不是在 dbo 架构下的存储的过程的问题。
* 修复了问题导致省略 AAD 连接服务器事件日志中的 Azure AD 返回的 TrackingId 属性。 如果 Azure AD Connect 会从 Azure AD 收到一个重定向消息，Azure AD Connect 无法连接到提供的终结点，则会发生此问题。 TrackingId 由支持工程师用于在故障排除过程使用服务端日志关联。
* Azure AD Connect 从 Azure AD 收到 LargeObject 错误时，将生成 EventID 为 6941 的事件，并返回消息“预配的对象太大。请减少此对象上属性值的数目。” 同时，Azure AD Connect 还会生成误导性事件 EventID 6900 和消息“Microsoft.Online.Coexistence.ProvisionRetryException: 无法通信与 Windows Azure Active Directory 服务。” 为了尽量减少混淆，Azure AD Connect 将不再生成后一种事件收到 LargeObject 错误时。
* 修复了当尝试更新通用 LDAP 连接器的配置时，Synchronization Service Manager 无法响应的问题。

**新功能/改进：**

Azure AD Connect 同步
* 同步规则更改 – 实现了以下同步规则更改：
  * 如果属性有 15 个以上的值，更新的默认同步规则设置为不导出属性 **userCertificate** 和 **userSMIMECertificate**。
  * AD 属性 **employeeID** 和 **msExchBypassModerationLink** 现在包含在默认同步规则集中。
  * AD 属性 **photo** 已从默认同步规则集中删除。
  * 添加 **preferredDataLocation** 到 Metaverse 架构和 AAD 连接器架构。 客户想要在 Azure AD 中更新任一属性可以实现自定义同步规则，可以这样做。 若要了解有关该属性的详细信息，请参阅文章部分 [Azure AD Connect 同步：如何更改默认配置 - 启用 PreferredDataLocation 同步](active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-preferreddatalocation)。
  * 添加 **userType** 到 Metaverse 架构和 AAD 连接器架构。 客户想要在 Azure AD 中更新任一属性可以实现自定义同步规则，可以这样做。

* Azure AD Connect 现在会自动启用的源定位点属性中 ConsistencyGuid 属性用作本地 AD 对象进一步，Azure AD Connect 填充具有 objectGuid 属性值的 ConsistencyGuid 属性是否为空。 此功能非常适用于新的部署。 若要了解有关此功能的详细信息，请参阅文章部分 [Azure AD Connect：设计概念 - 将 msDS-ConsistencyGuid 用作 sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor)。
* 新故障排除 cmdlet Invoke-ADSyncDiagnostics 已添加以帮助诊断密码哈希同步相关的问题。
* Azure AD Connect 现在支持同步已启用邮件公用文件夹对象从本地 AD 到 Azure AD。 可以启用功能使用在可选功能下的 Azure AD Connect 向导。
* Azure AD Connect 需要 AD DS 帐户从同步本地 AD。 以前，如果安装 Azure AD Connect 使用快速模式，可以提供企业管理员帐户的凭据。 Azure AD Connect 和离开它到 Azure AD Connect 创建的 AD DS 帐户所必需。 但是，对于自定义安装和将林添加到现有部署，必须提供 AD DS 帐户相反。 现在，还可以选择自定义安装过程中提供企业管理员帐户的凭据，并允许创建 AD DS 帐户所需的 Azure AD Connect。
* Azure AD Connect 现在支持 SQL AOA。 安装 Azure AD Connect 之前，必须启用 SQL。 在安装期间，Azure AD Connect 检测或不是否提供的 SQL 实例已启用 SQL AOA。 如果启用了 SQL AOA，Azure AD Connect 进一步指出如果 SQL AOA 配置为使用同步复制或异步复制。 当设置可用性组侦听器，建议将 RegisterAllProvidersIP 属性设置为 0。 这是因为 Azure AD Connect 当前使用 SQL Native Client 连接到 SQL 和 SQL Native Client 不支持使用 MultiSubNetFailover 属性。
* 如果将 LocalDB 用作 Azure AD Connect 的数据库并且已达到该数据库的 10-GB 限制，同步服务不再启动。 以前，需要执行 ShrinkDatabase LocalDB 回收足够的数据库空间要启动同步服务操作。 后来，可以使用同步服务管理器删除以回收更多的数据库空间的运行历史记录。 现在，可以使用开始 ADSyncPurgeRunHistory cmdlet 运行的清除历史记录数据从 LocalDB 回收数据库空间。 此外，此 cmdlet 支持脱机模式（通过指定 -offline 参数）的同步服务未运行时就可以使用。 注意：脱机模式下只能如果同步服务未运行，并且使用的数据库是 LocalDB。
* 若要减少存储所需的空间，Azure AD Connect 会将同步错误详细信息现在压缩之前将它们存储在 LocalDB/SQL 数据库。 从 Azure AD Connect 的较旧版本升级到此版本，Azure AD Connect 将执行一次性压缩现有同步错误详细信息。
* 以前，在更新后 OU 筛选配置，必须手动运行完全导入以确保现有对象都正确包括/排除从目录同步。 现在，Azure AD Connect 将自动触发完全导入期间下次同步周期。 此外，完整导入是仅应用于受更新 AD 连接器。 注意：此项改进是适用于筛选使用仅在 Azure AD Connect 向导所做的更新的 OU。 不适用于筛选使用 Synchronization Service Manager 所做的更新的 OU。
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
   3. 使用的是随 Azure AD Connect 一起安装的默认 SQL Server 2012 Express LocalDB，而不是你自己的完整 SQL。

**已解决的问题：**

Azure AD Connect 同步
* 修复了当一个或多个连接器缺少某个同步步骤的运行配置文件时，同步计划程序会完全跳过这一同步步骤的问题。 例如，使用 Synchronization Service Manager 手动添加了连接器，但没有为其创建增量导入运行配置文件。 此修补程序可以确保同步计划程序继续运行其他连接器的增量导入。
* 修复了当 Synchronization Service 在运行步骤的其中一步遇到问题时，会立即停止处理运行配置文件的问题。 此修补程序可以确保 Synchronization Service 会跳过该运行步骤，并继续处理其余步骤。 例如，具有多个运行步骤的 AD 连接器的增量导入运行配置文件（每个本地 AD 域一个步骤）。 即使其中一个 AD 域出现网络连接问题，Synchronization Service 也会运行其他 AD 域的增量导入。
* 修复了自动升级期间会跳过 Azure AD 连接器更新的问题。
* 修复了安装过程中 Azure AD 连接器不能正确识别服务器是否是域控制器，进而导致 DirSync 升级失败的问题。
* 修复了 DirSync 就地升级不会为 Azure AD 连接器创建任何运行配置文件的问题。
* 修复了当尝试配置通用的 LDAP 连接器时，Synchronization Service Manager 用户界面无法响应的问题。

AD FS 管理
* 修复了如果 AD FS 主节点已移至其他服务器，Azure AD Connect 向导将失败的问题。

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
* Azure AD 租户有一个服务配置，该配置指示了是否已为租户启用密码同步功能。 之前，当你同时拥有活动和暂存服务器时，Azure AD Connect 很容易错误配置服务配置。 现在，Azure AD Connect 会尝试让服务配置只和活动 Azure AD Connect 服务器保持一致。
* 如果本地 AD 未启用 AD 回收站，Azure AD Connect 向导会检测并返回警告。
* 之前，如果批处理的对象总大小超过特定阈值，导出到 Azure AD 会超时并失败。 现在遇到此问题时，Synchronization Service 会再次尝试以较小批次单独重新发送对象。
* 已经将 Synchronization Service Key Management 应用程序从 Windows 开始菜单中删除。 仍然支持使用 miiskmu.exe 通过命令行接口管理加密密钥。 有关加密密钥的详细信息，请参阅文章 [Abandoning the Azure AD Connect Sync encryption key](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key)（弃用 Azure AD Connect 同步加密密钥）。
* 之前，如果更改了 Azure AD Connect 同步服务帐户密码，则无法正常启动 Synchronization Service，除非你已弃用加密密钥并重新初始化 Azure AD Connect 同步服务帐户密码。 现在不再需要此操作。

桌面 SSO

* 配置传递身份验证和桌面 SSO 时，Azure AD Connect 向导不再需要在网络上打开端口 9090。 只需要端口 443。 

## <a name="114430"></a>1.1.443.0
发布日期：2017 年 3 月

**已解决的问题：**

Azure AD Connect 同步
* 修复了在 Azure AD 连接器的显示名称没有包含分配给 Azure AD 租户的初始 onmicrosoft.com 域时，Azure AD Connect 向导将失败的问题。
* 修复了在同步服务帐户的密码包含特殊字符（如撇号、冒号和空格）的情况下，在与 SQL 数据库进行连接时，Azure AD Connect 向导将失败的问题。
* 修复了在暂时不同步某个本地 AD 对象，然后再将其进行同步后，暂存模式下的 Azure AD Connect 服务器上将出现“该 dimage 具有不同于映像的定位点”错误的问题。
* 修复了在暂时不同步某个本地 AD 对象，然后再将其进行同步后，暂存模式下的 Azure AD Connect 服务器上将出现“DN 定位的对象是一个幻影”错误的问题。

AD FS 管理
* 修复了在配置备用登录 ID 后，Azure AD Connect 向导不会更新 AD FS 配置并设置对信赖方信任的正确声明的问题。
* 修复了 Azure AD Connect 向导无法正确处理 AD FS 服务器（该服务器的服务帐户是通过 userPrincipalName 格式设置的，而非 sAMAccountName 格式）的问题。

直通身份验证
* 修复了在选择了“直通身份验证”但其连接器注册失败时，Azure AD Connect 向导将失败的问题。
* 修复了在启用了桌面 SSO 功能时，Azure AD Connect 向导将绕过对所选登录方法的验证检查。

密码重置
* 修复了这样一个问题：当连接已由防火墙或代理终止时，该问题可能会导致 Azure AAD Connect 服务器不尝试重新连接。

**新功能/改进：**

Azure AD Connect 同步
* Get-ADSyncScheduler cmdlet 现在可返回一个名为 SyncCycleInProgress 的新的布尔属性。 如果返回的值为 true，则意味着正在进行计划的同步周期。
* 已将用于存储 Azure AD Connect 安装和安装程序日志的目标文件夹从 %localappdata%\AADConnect 移至 %programdata%\AADConnect，以提高日志文件的访问性。

AD FS 管理
* 添加了对更新 AD FS 场 SSL 证书的支持。
* 添加了对管理 AD FS 2016 的支持。
* 现在可以在 AD FS 安装期间指定现有 gMSA（组托管服务帐户）。
* 现在可以将 SHA-256 配置为 Azure AD 信赖方信任的签名哈希算法。

密码重置
* 引入了多项改进，以允许产品在具有更严格防火墙规则的环境中正常工作。
* 提高了与 Azure 服务总线的连接可靠性。

## <a name="113800"></a>1.1.380.0
发布日期：2016 年 12 月

**修复的问题：**

* 修复了本版本中缺少针对 Active Directory 联合身份验证服务 (AD FS) 的 issuerid 声明规则的问题。

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

## <a name="113710"></a>1.1.371.0
发布日期：2016 年 12 月

**已知问题：**

* 本版本中缺少针对 AD FS 的 issuerid 声明规则。 若要将多个域与 Azure Active Directory (Azure AD) 联合，需使用 issuerid 声明规则。 如果使用 Azure AD Connect 管理本地 AD FS 部署，则升级到此版本将从 AD FS 配置中删除现有 issuerid 声明规则。 可在安装/升级后添加 issuerid 声明规则来解决此问题。 有关添加 issuerid 声明规则的详细信息，请参阅[与 Azure AD 联合的多域支持](active-directory-aadconnect-multiple-domains.md)一文。

**修复的问题：**

* 如果未打开用于出站连接的端口 9090，Azure AD Connect 安装或升级将会失败。

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

## <a name="113700"></a>1.1.370.0
发布日期：2016 年 12 月

**已知问题：**

* 本版本中缺少针对 AD FS 的 issuerid 声明规则。 若要将多个域与 Azure AD 联合，需使用 issuerid 声明规则。 如果使用 Azure AD Connect 管理本地 AD FS 部署，则升级到此版本将从 AD FS 配置中删除现有 issuerid 声明规则。 可在安装/升级后添加 issuerid 声明规则来解决此问题。 有关添加 issuerid 声明规则的详细信息，请参阅[与 Azure AD 联合的多域支持](active-directory-aadconnect-multiple-domains.md)一文。
* 必须打开用于出站连接的端口 9090 才能完成安装。

**新功能：**

* 直通身份验证（预览）。

>[!NOTE]
>不通过 Azure AD Connect 自动升级功能向客户提供此内部版本。

## <a name="113430"></a>1.1.343.0
发布日期：2016 年 11 月

**已知问题：**

* 本版本中缺少针对 AD FS 的 issuerid 声明规则。 若要将多个域与 Azure AD 联合，需使用 issuerid 声明规则。 如果使用 Azure AD Connect 管理本地 AD FS 部署，则升级到此版本将从 AD FS 配置中删除现有 issuerid 声明规则。 可在安装/升级后添加 issuerid 声明规则来解决此问题。 有关添加 issuerid 声明规则的详细信息，请参阅[与 Azure AD 联合的多域支持](active-directory-aadconnect-multiple-domains.md)一文。

**已解决的问题：**

* 有时，由于无法创建密码符合组织密码策略指定的复杂性级别的本地服务帐户，安装 Azure AD Connect 失败。
* 解决了当连接器空间中的某个对象既在一个联接规则的范围以外，同时又在另一个联接规则的范围以内时，无法重新评估联接规则的问题。 如果两个或更多个联接规则的联接条件互斥，则可能会发生此问题。
* 解决了当（Azure AD 中）不包含联接规则的入站同步规则的优先级值低于包含联接规则的入站同步规则时，不处理前一种规则的问题。

**改进：**

* 添加了在 Windows Server 2016 标准版或更高版本上安装 Azure AD Connect 的支持。
* 添加了将 SQL Server 2016 用作 Azure AD Connect 远程数据库的支持。

## <a name="112810"></a>1.1.281.0
发布日期：2016 年 8 月

**已解决的问题：**

* 只有在下一个同步周期完成后，才对同步间隔进行更改。
* Azure AD Connect 向导不接受用户名开头为下划线 (\_) 的 Azure AD 帐户。
* 如果帐户密码包含太多特殊字符，Azure AD Connect 向导无法对提供的 Azure AD 帐户进行身份验证。 此时会返回错误消息“无法验证凭据。 发生意外错误” 返回。
* 卸载暂存服务器会在 Azure AD 租户中禁用密码同步，导致活动服务器的密码同步失败。
* 在用户未存储密码哈希的罕见情况下，密码同步失败。
* 当 Azure AD Connect 服务器启用暂存模式时，不会暂时禁用密码写回。
* 当服务器处于暂存模式时，Azure AD Connect 向导不会显示实际的密码同步和密码写回配置， 而始终将这些配置显示为已禁用。
* 当服务器处于暂存模式时，Azure AD Connect 向导不会保存密码同步和密码写回的配置更改。

**改进：**

* 已更新 Start-ADSyncSyncCycle cmdlet，指出是否能够成功启动新的同步周期。
* 已添加 Stop-ADSyncSyncCycle cmdlet，终止当前正在进行的同步周期和操作。
* 已更新 Stop-ADSyncScheduler cmdlet，终止当前正在进行的同步周期和操作。
* 在 Azure AD Connect 向导中配置[目录扩展](active-directory-aadconnectsync-feature-directory-extensions.md)时，现在可选择“Teletex 字符串”类型的 Azure AD 属性。

## <a name="111890"></a>1.1.189.0
发布日期：2016 年 6 月

**已解决的问题和改进：**

* Azure AD Connect 现在可以安装于符合 FIPS 的服务器上。
  * 有关密码同步，请参阅[密码同步和 FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)。
* 已修复下列问题：NetBIOS 名称无法解析为 Active Directory 连接器中的 FQDN。

## <a name="111800"></a>1.1.180.0
发布日期：2016 年 5 月

**新功能：**

* 警告并帮助你验证域（如果运行 Azure AD Connect 之前未执行此操作）。
* 添加了对[德国 Microsoft 云](active-directory-aadconnect-instances.md#microsoft-cloud-germany)的支持。
* 添加了对最新 [Microsoft Azure 政府云](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud)基础结构的支持，以及新的 URL 要求。

**已解决的问题和改进：**

* 在同步规则编辑器中添加了筛选功能以方便查找同步规则。
* 改进了删除连接器空间时的性能。
* 修复了在同一个运行轮次中同时删除和添加（称为删除/添加）同一个对象时出现的问题。
* 在升级或刷新目录架构时，已禁用的同步规则不再重新启用包含的对象和属性。

## <a name="111300"></a>1.1.130.0
发布日期：2016 年 4 月

**新功能：**

* 添加了对[目录扩展](active-directory-aadconnectsync-feature-directory-extensions.md)的多值属性支持。
* 添加了将[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)的更多配置变体视为符合升级要求的支持。
* 为[自定义计划程序](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler)添加了一些 cmdlet。

## <a name="111190"></a>1.1.119.0
发布日期：2016 年 3 月

**已解决的问题：**

* 确定 Windows Server 2008（R2 之前的版本）上无法使用快速安装，因为此操作系统不支持密码同步。
* 使用自定义筛选器配置从 DirSync 升级无法按预期进行。
* 升级到较新版本且没有进行任何配置更改时，不应计划完全导入/同步。

## <a name="111100"></a>1.1.110.0
发布日期：2016 年 2 月

**已解决的问题：**

* 如果安装不位于默认的 C:\Program Files 文件夹中，则无法从旧版升级。
* 如果进行安装，并在安装向导结束时清除“启动同步过程”，再次运行安装向导将不启用计划程序。
* 在日期/时间格式并非美国英语的服务器上，计划程序将无法正常运行。 此外，还会阻止 `Get-ADSyncScheduler` 返回正确的时间。
* 如果以 AD FS 作为登录选项和升级来安装旧版 Azure AD Connect，便无法再次运行安装向导。

## <a name="111050"></a>1.1.105.0
发布日期：2016 年 2 月

**新功能：**

* 适用于快速设置客户的[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)功能。
* 使用安装向导中的 Azure 多重身份验证和 Privileged Identity Management 来提供全局管理员支持。
  * 如果使用多重身份验证，则代理也需要允许发往 https://secure.aadcdn.microsoftonline-p.com 的流量。
  * 需将 https://secure.aadcdn.microsoftonline-p.com 添加到受信任的站点列表，这样多重身份验证才能正常工作。
* 允许在初始安装之后更改用户的登录方法。
* 允许在安装向导中使用[域和 OU 筛选](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。 这也允许连接到并非所有域都可供使用的林。
* [计划程序](active-directory-aadconnectsync-feature-scheduler.md)是同步引擎的内置功能。

**从预览版升级到 GA 的功能：**

* [设备写回](active-directory-aadconnect-feature-device-writeback.md)。
* [目录扩展](active-directory-aadconnectsync-feature-directory-extensions.md)。

**新的预览功能：**

* 新的默认同步周期间隔为 30 分钟。 过去所有旧版本都是 3 小时。 添加了对更改[计划程序](active-directory-aadconnectsync-feature-scheduler.md)行为的支持。

**已解决的问题：**

* 验证 DNS 域页面不一定都能识别域。
* 配置 AD FS 时出现域管理员凭据提示。
* 当本地 AD 帐户所在域的 DNS 树与根域不同时，安装向导将无法识别这些帐户。

## <a name="1091310"></a>1.0.9131.0
发布日期：2015 年 12 月

**已解决的问题：**

* 更改 Active Directory 域服务 (AD DS) 中的密码时，密码同步可能不会正常工作，但设置密码时可以正常工作。
* 如果设置了代理服务器，在安装期间或者在配置页上取消升级时，向 Azure AD 进行身份验证可能会失败。
* 如果你不是 SQL Server 系统管理员 (SA)，从装有完整 SQL Server 实例的旧版 Azure AD Connect 更新将会失败。
* 从装有远程 SQL Server 的旧版 Azure AD Connect 更新时，将显示错误消息“无法访问 ADSync SQL 数据库”。

## <a name="1091250"></a>1.0.9125.0
发布日期：2015 年 11 月

**新功能：**

* 可将 AD FS 重新配置为 Azure AD 信任。
* 可以刷新 Active Directory 架构和重新生成同步规则。
* 可以禁用同步规则。
* 可将“AuthoritativeNull”定义为同步规则中的新文本。

**新的预览功能：**

* [用于同步的 Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md)。
* 支持 [Azure AD 域服务](../active-directory-passwords-update-your-own-password.md)密码同步。

**新的受支持方案：**

* 支持多个本地 Exchange 组织。 有关详细信息，请参阅[包含多个 Active Directory 林的混合部署](https://technet.microsoft.com/library/jj873754.aspx)。

**已解决的问题：**

* 密码同步问题：
  * 从范围外移到范围内的对象不会同步其密码。 这包括 OU 和属性筛选。
  * 选择要包含在同步中的新 OU 时不需要完全密码同步。
  * 启用已禁用的用户时密码不会同步。
  * 密码重试队列是无限的，以前实施的 5000 个对象限制已停用且已被删除。
<<<<<<< HEAD <<<<<<< HEAD
  * [改进了故障排除](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。
=======
  * [改进了故障排除](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。
>>>>>>> <a name="487b660b6d3bb5ce9e64b6fdbde2ae621cb91922"></a>487b660b6d3bb5ce9e64b6fdbde2ae621cb91922
=======
  * [改进了故障排除](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。
>>>>>>> 4b2e846c2cd4615f4e4be7195899de11e3957c83
* 无法连接到具有 Windows Server 2016 林功能级别的 Active Directory。
* 初始安装后，无法更改用于组筛选的组。
* 对于在启用密码写回的情况下执行密码更改的每个用户，不再能够在 Azure AD Connect 服务器上创建新的用户配置文件。
* 无法在同步规则范围内使用长整数值。
* 如果有无法访问的域控制器，“设备写回”复选框将保持禁用状态。

## <a name="1086670"></a>1.0.8667.0
发布日期：2015 年 8 月

**新功能：**

* Azure AD Connect 安装向导现已本地化为所有 Windows Server 语言。
* 添加了在使用 Azure AD 密码管理时的帐户解锁支持。

**已解决的问题：**

* 如果另一位用户而不是第一位启动安装的人继续安装，则 Azure AD Connect 安装向导将会崩溃。
* 如果 Azure AD Connect 的先前卸载操作无法将 Azure AD Connect Sync 完全卸载，则无法重新安装。
* 如果用户不在林的根域中或使用了非英文版 Active Directory，则无法使用快速安装选项安装 Azure AD Connect。
* 如果无法解析 Active Directory 用户帐户的 FQDN，则会显示“无法提交架构”的误导性错误消息。
* 如果 Active Directory 连接器上使用的帐户已在向导外部更改，向导在进行后续操作时将会失败。
* Azure AD Connect 有时无法在域控制器上安装。
* 如果添加了扩展属性，则无法启用和禁用“暂存模式”。
* 由于 Active Directory 连接器上的密码不正确，某些配置中的密码写回失败。
* 如果属性筛选中使用可分辨名称 (DN)，则无法升级 DirSync。
* 使用密码重置时 CPU 使用率过高。

**已删除的预览版功能：**

* 根据预览版客户的反馈，已暂时删除 [用户写回](active-directory-aadconnect-feature-preview.md#user-writeback) 预览版功能。 今后在解决所提供的反馈意见后，我们将再次添加此功能。

## <a name="1086410"></a>1.0.8641.0
发布日期：2015 年 6 月

**Azure AD Connect 的初始版本。**

名称从 Azure AD Sync 更改为 Azure AD Connect。

**新功能：**

* [快速设置](active-directory-aadconnect-get-started-express.md)安装
* 可以[配置 AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* 可[从 DirSync 升级](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* 引入了[暂存模式](active-directory-aadconnectsync-operations.md#staging-mode)

**新的预览功能：**

* [用户写回](active-directory-aadconnect-feature-preview.md#user-writeback)
* [组写回](active-directory-aadconnect-feature-preview.md#group-writeback)
* [设备写回](active-directory-aadconnect-feature-device-writeback.md)
* [目录扩展](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
发布日期：2015 年 5 月

**新要求：**

* Azure AD Sync 现在要求安装 .NET framework 版本 4.5.1。

**已解决的问题：**

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

**新功能：**

* 联接现在可以连接到 MV 中的“任何”对象类型。

## <a name="104850222"></a>1.0.485.0222
发布日期：2015 年 2 月

**改进：**

* 改进了导入性能。

**已解决的问题：**

* 密码同步具有属性筛选所用的 cloudFiltered 属性。 已筛选的对象将不再在密码同步范围中。
* 在拓扑中有大量域控制器的极少数情况下，密码同步不起作用。
* 在设备管理后从 Azure AD 连接器导入时，已在 Azure AD/Intune 中启用了“Stopped-server”。
* 从同一林中的多个域联接外部安全主体 (FSP) 会导致模糊联接错误。

## <a name="104751202"></a>1.0.475.1202
发布日期：2014 年 12 月

**新功能：**

* 现在支持使用基于属性的筛选执行密码同步。 有关详细信息，请参阅[使用筛选进行密码同步](active-directory-aadconnectsync-configure-filtering.md)。
* msDS-ExternalDirectoryObjectID 属性将写回 Active Directory。 此功能添加了对 Office 365 应用程序的支持。 它使用 OAuth2 访问混合 Exchange 部署中的联机邮箱和本地邮箱。

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

**新功能：**

* 从多个本地 Active Directory 到 Azure AD 的密码同步。
* 已将安装 UI 本地化为所有的 Windows Server 语言。

**从 AADSync 1.0 GA 升级**

如果已安装 Azure AD Sync，则还必须执行另外一个步骤（考虑到可能已更改现成的同步规则）。 在升级到 1.0.470.1023 版之后，已修改的同步规则将被复制。 对于每个已修改的同步规则，请执行以下操作：

1.  找到已修改的同步规则，并记下所做的更改。
* 删除同步规则。
* 找到由 Azure AD Sync 创建的新同步规则，然后重新应用所做的更改。

**Active Directory 帐户的权限**

必须为 Active Directory 帐户授予其他权限，才能从 Active Directory 读取密码哈希。 要授予的权限称为“复制目录更改”和“复制目录更改所有项”。 需要这两个权限才能读取密码哈希。

## <a name="104190911"></a>1.0.419.0911
发布日期：2014 年 9 月

**Azure AD Sync 的初始版本。**

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。


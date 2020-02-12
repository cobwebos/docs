---
title: Azure Active Directory Connect 常见问题解答 | Microsoft Docs
description: 本文解答有关 Azure AD Connect 的常见问题。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149807"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect 常见问题解答

## <a name="general-installation"></a>常规安装

**问：如何强化 Azure AD Connect 服务器以减少安全攻击面？**

Microsoft 建议强化 Azure AD Connect 服务器，以降低 IT 环境关键组件的安全攻击面。  遵循以下建议将减少组织面临的安全风险。

* 将 Azure AD Connect 部署在已加入域的服务器上，并限制对域管理员或其他严格控制的安全组的管理访问权限

若要了解更多信息，请参阅以下文章： 

* [保护管理员组](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [保护内置管理员帐户](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [减少攻击面，提高安全性并 sustainment](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [降低 Active Directory 攻击面](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**问：如果 Azure Active Directory (Azure AD) 全局管理员已启用双重身份验证 (2FA)，安装是否能够正常进行？**  
2016 年 2 月版开始支持此方案。

**问：Azure AD Connect 是否提供无人值守安装方法？**  
仅支持使用安装向导来安装 Azure AD Connect。 不支持无人值守的静默安装。

**问：我有一个林，其中无法联系到一个域。如何实现安装 Azure AD Connect？**  
2016 年 2 月版开始支持此方案。

**问：Azure Active Directory 域服务 (Azure AD DS) 运行状况代理是否能够在服务器核心上运行？**  
是的。 安装代理后，可以使用以下 PowerShell cmdlet 完成注册过程： 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**问： Azure AD Connect 是否支持从两个域同步到 Azure AD？**  
是的。支持此方案。 请参阅[多个域](how-to-connect-install-multiple-domains.md)。
 
**问：是否可对 Azure AD Connect 中的同一个 Active Directory 域使用多个连接器？**  
不支持对同一个 AD 域使用多个连接器。 

**问：是否可将 Azure AD Connect 数据库从本地数据库移到远程 SQL Server 实例？**    
是的，以下步骤提供了此操作的一般指导。 我们目前正在努力编写更详细的文档。
1. 备份 LocalDB ADSync 数据库。
最简单的方法就是使用 Azure AD Connect 所在的同一台计算机上安装的 SQL Server Management Studio。 连接到 *(LocalDb).\ADSync*，然后备份 ADSync 数据库。

2. 将 ADSync 数据库还原到远程 SQL Server 实例。

3. 针对现有的[远程 SQL 数据库](how-to-connect-install-existing-database.md)安装 Azure AD Connect。
   本文演示了如何改用本地 SQL 数据库。 如果改用远程 SQL 数据库，则在此过程的步骤 5 中，还必须输入用于运行 Windows 同步服务的现有服务帐户。 下面描述了此同步引擎服务帐户：
   
      **使用现有的服务帐户**：默认情况下，Azure AD Connect 将虚拟服务帐户用于为要使用的同步服务。 如果使用远程 SQL Server 实例或使用需要身份验证的代理，请使用托管服务帐户，或者使用域中的服务帐户并知道密码。 在这些情况下，请输入要使用的帐户。 确保运行安装的用户是 SQL 中的系统管理员，以便可以创建服务帐户的登录凭据。 有关详细信息，请参阅 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md#adsync-service-account)。 
   
      现在，在使用最新版本的情况下，可以由 SQL 管理员在带外进行数据库预配，然后由具有数据库所有者权限的 Azure AD Connect 管理员完成安装。 有关详细信息，请参阅[使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)。

为简单起见，我们建议安装 Azure AD Connect 的用户是 SQL 中的系统管理员。 但是，在最新的版本中，现在也可以根据[使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md) 中所述，使用委派的 SQL 管理员。

**问：现场的一些最佳做法是什么？**  

下面是一个信息性文档，其中提供了工程、支持和我们的顾问多年开发的一些最佳实践。  这会显示在可以快速引用的项目符号列表中。  虽然此列表会尝试全面，但可能还有其他一些可能尚未在列表中进行此操作的最佳实践。

- 如果使用完整 SQL，则它应保留在本地与远程
    - 更少跃点
    - 更易于进行故障排除
    - 复杂性较低
    - 需要为 SQL 指定资源并允许 Azure AD Connect 和操作系统的开销
- 绕过代理如果可能，如果你无法绕过代理，则需要确保超时值大于5分钟。
- 如果需要代理，则必须将代理添加到 machine.config 文件
- 请注意本地 SQL 作业和维护及其对 Azure AD Connect 的影响，尤其是重新编制索引
- 确保不能在外部解析 DNS
- 无论你使用的是物理服务器还是虚拟服务器，请确保[服务器规范](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect)按建议
- 如果你使用的是需要专用资源的虚拟服务器，请确保
- 确保磁盘和磁盘配置符合 SQL Server 的最佳实践
- 安装和配置用于监视的 Azure AD Connect Health
- 使用内置 Azure AD Connect 的删除阈值。
- 仔细查看要为所有更改准备的版本更新和可能添加的新属性
- 备份一切
    - 备份密钥
    - 备份同步规则
    - 备份服务器配置
    - 备份 SQL 数据库
- 确保在没有 SQL VSS 编写器的情况下，没有任何第三方备份代理在备份 SQL （在具有第三方快照的虚拟服务器中常见）
- 限制在增加复杂性时使用的自定义同步规则数量
- 将 Azure AD Connect 服务器视为第0层服务器
- Leery 修改云同步规则，而无需充分了解影响和正确的业务驱动因素
- 请确保打开正确的 URL 和防火墙端口，以便支持 Azure AD Connect 和 Azure AD Connect Health
- 利用云筛选属性来排查和阻止虚拟对象
- 使用过渡服务器时，请确保使用的是 Azure AD Connect 的配置文档服务器，以便在服务器之间保持一致性
- 过渡服务器应位于不同的数据中心（物理位置）
- 过渡服务器并不是一种高可用性解决方案，但可以有多个过渡服务器
- 引入 "延迟" 过渡服务器可在出现错误的情况下减少某些可能的停机时间
- 首先测试并验证过渡服务器上的所有升级
- 在切换到过渡服务器之前始终验证导出。  利用过渡服务器进行完全导入和完全同步，以降低业务影响
- 尽可能保持 Azure AD Connect 服务器之间的版本一致性 

**问：我是否可以允许 Azure AD Connect 在工作组计算机上创建 Azure AD 连接器帐户？**
不是。  为了允许 Azure AD Connect 自动创建 Azure AD 连接器帐户，计算机必须已加入域。  

## <a name="network"></a>网络
**问：我的防火墙、网络设备或其他设备限制了连接在网络上保持打开状态的时间。使用 Azure AD Connect 时，我的客户端的超时阈值是多少？**  
所有网络软件、物理设备或其他软硬件限制最长连接时间的阈值应该至少为 5 分钟 (300 秒)，使装有 Azure AD Connect 客户端的服务器能够与 Azure Active Directory 连接。 此项建议同样适用于以前发布的 Microsoft 标识同步工具。

**是否支持单一标签域 (SLD)？**  
虽然我们强烈建议不要使用此网络配置（[请参阅相关文章](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)），但只要单级域的网络配置正常发挥作用，将 Azure AD Connect 同步与单标签域配合使用就是受支持的。

**问：是否支持具有非连续 AD 域的林？**  
Azure AD Connect 不支持包含非连续命名空间的本地林。

**问：是否支持包含句点的 NetBIOS 名称？**  
Azure AD Connect 不支持 NetBIOS 名称包含点号 (.) 的本地林或域。

**问：是否支持纯 IPv6 环境？**  
Azure AD Connect 不支持纯 IPv6 环境。

**问：具有多林环境，而两个林之间的网络使用 NAT （网络地址转换）。是否在受支持的两个林之间使用 Azure AD Connect？**</br>
否，不支持通过 NAT 使用 Azure AD Connect。 

## <a name="federation"></a>联合
**问：如果我收到一封电子邮件，要求我续订 Office 365 证书，我该怎么办？**  
有关续订证书的指导，请参阅[续订证书](how-to-connect-fed-o365-certs.md)。

**问：我为 Office 365 信赖方设置了 "自动更新信赖方"。当我的令牌签名证书自动滚动时，我是否需要采取任何措施？**  
请参考[续订证书](how-to-connect-fed-o365-certs.md)一文中所述的指导。

## <a name="environment"></a>环境
**问：安装 Azure AD Connect 之后，是否支持重命名服务器？**  
不是。 更改服务器名称将导致同步引擎无法连接到 SQL 数据库实例，并且服务将无法启动。

**问：是否在启用 FIPS 的计算机上支持下一代加密（NGC）同步规则？**  
不是。  它们不受支持。

**问：如果我在 Azure 门户中禁用了同步的设备（例如： HAADJ），为什么会重新启用它？**<br>
同步的设备可能会在本地创作或掌控。 如果在本地启用了同步的设备，即使管理员之前禁用了同步设备，也可能会在 Azure 门户中重新启用它。 若要禁用已同步的设备，请使用本地 Active Directory 禁用计算机帐户。

**问：如果我在 Office 365 或 Azure AD 门户中阻止用户登录到已同步的用户，该用户在重新登录时不会阻止用户登录？**<br>
同步的用户可能会在本地创作或掌控。 如果帐户在本地启用，则它可以取消阻止管理员所放置的登录块。

## <a name="identity-data"></a>标识数据
**问：Azure AD 中的 userPrincipalName (UPN) 属性为何与本地 UPN 不匹配？**  
有关信息，请参阅以下文章：

* [Office 365、Azure 或 Intune 中的用户名与本地 UPN 或备用登录 ID 不匹配](https://support.microsoft.com/kb/2523192)
* [在将用户帐户的 UPN 更改为使用不同的联合域后，Azure Active Directory 同步工具未同步更改](https://support.microsoft.com/kb/2669550)

还可以根据 [Azure AD Connect 同步服务功能](how-to-connect-syncservice-features.md)中所述配置 Azure AD，以允许同步引擎更新 UPN。

**问：是否支持本地 Azure AD 组或联系人对象与现有 Azure AD 组或联系人对象的软匹配？**  
是，这种软匹配取决于 proxyAddress。 未启用邮件的组不支持软匹配。

**问：是否支持手动设置现有 Azure AD 组或联系人对象的 ImmutableId 属性，以将其硬匹配到本地 Azure AD 组或联系人对象？**  
目前不支持在现有的 Azure AD 组或联系人对象中手动设置 ImmutableId 属性，以硬匹配该对象。

## <a name="custom-configuration"></a>自定义配置
**问：在哪里可以找到 Azure AD Connect 的 PowerShell cmdlet 介绍？**  
仅支持客户使用本站点上介绍的 cmdlet，而不支持使用 Azure AD Connect 中的其他 PowerShell cmdlet。

**问：是否可以使用 Synchronization Service Manager 中的“服务器导出/服务器导入”选项在服务器之间移动配置？**  
不是。 此选项不会检索所有配置设置，因此不应使用。 请改用向导在第二台服务器上创建基础配置，并使用同步规则编辑器生成 PowerShell 脚本，如此即可在服务器之间移动任何自定义规则。 有关详细信息，请参阅[交叉迁移](how-to-upgrade-previous-version.md#swing-migration)。

**问：是否可以为 Azure 登录页缓存密码，这是否会因为包含一个具有 *autocomplete = "false"* 属性的密码输入元素而阻止此缓存？**  
目前不支持修改“密码”字段的 HTML 属性，包括 autocomplete 标记。 我们目前正在开发一种功能，它将允许使用自定义 JavaScript 向“密码”字段添加任何属性。

**问： Azure 登录页显示已成功登录的用户的用户名。此行为是否可以关闭？**  
目前不支持修改“密码”输入字段的 HTML 属性，包括 autocomplete 标记。 我们目前正在开发一种功能，它将允许使用自定义 JavaScript 向“密码”字段添加任何属性。

**问：是否有方法来阻止并发会话？**  
不是。

## <a name="auto-upgrade"></a>自动升级

**问：使用自动升级有什么好处？其结果是什么？**  
建议所有客户为安装的 Azure AD Connect 启用自动升级。 好处是客户可以一直接收最新的修补程序，包括在 Azure AD Connect 中发现的漏洞的安全更新。 升级过程很轻松，只要有新版本发布就会自动进行。 每次发布新版本，成千上万的 Azure AD Connect 客户都会使用自动升级。

自动升级过程始终会先确定某个安装是否符合自动升级的条件。 如果符合条件，则会执行并测试升级。 此过程还包括查找对规则的自定义更改和特定的环境因素。 如果测试表明升级未成功，则会自动还原以前的版本。

根据环境大小，此过程可能需要数小时才能完成。 在升级过程中，不会在 Windows Server Active Directory 和 Azure AD 之间进行同步。

**问：我收到了一封电子邮件，告诉我自动升级不再工作，需要安装新版本。为什么需要这样做？**  
我们去年发布了一个 Azure AD Connect 版本，该版本在特定情况下会禁用服务器上的自动升级功能。 Azure AD Connect 1.1.750.0 版中已修复此问题。 如果你受此问题的影响，可通过以下方式进行缓解：运行一个 PowerShell 脚本来修复此问题，或者手动升级到最新版本的 Azure AD Connect。 

若要运行该 PowerShell 脚本，请[下载该脚本](https://aka.ms/repairaadconnect)，并在 PowerShell 管理窗口中的 Azure AD Connect 服务器上运行该脚本。 若要了解如何运行该脚本，请[观看此简短视频](https://aka.ms/repairaadcau)。

若要手动进行升级，必须下载并运行最新版的 AADConnect.msi 文件。
 
-  如果当前版本低于 1.1.750.0，请[下载并升级到最新版本](https://www.microsoft.com/download/details.aspx?id=47594)。
- 如果 Azure AD Connect 版本为 1.1.750.0 或更高，则不需要采取其他措施。 所用的版本已包含自动升级修复程序。 

**问：我收到了一封电子邮件，告诉我升级到最新版本以重新启用自动升级。我使用的是版本1.1.654.0。是否需要升级？**  
需要。需要升级到 1.1.750.0 或更高版本才能重新启用自动升级。 [下载并升级到最新版本](https://www.microsoft.com/download/details.aspx?id=47594)。

**问：我收到了一封电子邮件，告诉我升级到最新版本以重新启用自动升级。如果我使用 PowerShell 启用了自动升级，是否仍需要安装最新版本？**  
是的，仍需要升级到 1.1.750.0 或更高版本。 通过 PowerShell 启用自动升级服务不会解决在 1.1.750.0 之前的版本中发现的自动升级问题。

**问：我想要升级到较新版本，但不确定安装 Azure AD Connect 的用户，并且没有用户名和密码。是否需要这样做？**
不需要知道最初用来升级 Azure AD Connect 的用户名和密码。 可以使用任何具有全局管理员角色的 Azure AD 帐户。

**问：如何确定所用 Azure AD Connect 的版本？**  
若要确定安装在服务器上的 Azure AD Connect 的具体版本，请转到“控制面板”，然后选择“程序” **“程序和功能”并找到已安装的 Microsoft Azure AD Connect 版本，如下所示：**  > 

![控制面板中的 Azure AD Connect 版本](./media/reference-connect-faq/faq1.png)

**问：如何升级到最新版本的 Azure AD Connect？**  
若要了解如何升级到最新版本，请参阅 [Azure AD Connect：从旧版升级到最新版本](how-to-upgrade-previous-version.md)。 

**问：我们已在去年 Azure AD Connect 升级到最新版本。是否需要再次升级？**  
Azure AD Connect 团队会对该服务进行频繁的更新。 若要充分利用 Bug 修复、安全更新和新功能的优势，必须使用最新版本来保持服务器的最新状态。 如果启用自动升级，则会自动更新软件版本。 若要查找 Azure AD Connect 的版本发布历史记录，请参阅 [Azure AD Connect：版本发布历史记录](reference-connect-version-history.md)。

**问：执行升级需要多长时间？对我的用户有什么影响？**  
升级所需时间取决于租户大小。 对于大型组织而言，最好是在晚上或周末升级。 在升级期间不会发生同步活动。

**问：我相信升级到了 Azure AD Connect，但 Office 门户仍提到 DirSync。为什么要这样做呢？**  
Office 团队会更新 Office 门户，使之反映当前的产品名称。 它不会反映所用的同步工具。

**问：我的自动升级状态显示为 "已挂起"。为什么暂停？是否应启用它？**  
在以前的版本中存在一个 Bug，该 Bug 在特定情况下会将自动升级状态设置为“已暂停”。 手动启用它在技术上是可行的，但需要执行多个复杂的步骤。 最好是安装最新版本的 Azure AD Connect。

**问：我的公司拥有严格的更改管理要求，我想要控制何时推出。能否控制自动升级启动的时间？**  
不能。目前没有此类功能。 我们正在评估是否在将来的版本中推出此功能。

**问：如果自动升级失败，是否会收到一封电子邮件？我如何知道它已成功？**  
你不会收到升级结果的通知。 我们正在评估是否在将来的版本中推出此功能。

**问：你们是否会发布一个关于何时计划推出自动升级的时间线？**  
自动升级是新版本发布过程的第一个步骤。 只要有新版本，我们就会自动推送升级。 新版 Azure AD Connect 会在 [Azure AD 路线图](../fundamentals/whats-new.md)中预先公告。

**问：自动升级是否同时升级 Azure AD Connect Health？**  
是的，自动升级也会升级 Azure AD Connect Health。

**问：你们是否也会自动升级暂存模式下的 Azure AD Connect 服务器？**  
是的，可以自动升级暂存模式下的 Azure AD Connect 服务器。

**问：如果自动升级失败而 Azure AD Connect 服务器无法启动，该怎么办？**  
Azure AD Connect 服务偶尔会在升级以后无法启动。 在这种情况下，重新启动服务器通常就会解决问题。 如果 Azure AD Connect 服务仍然无法启动，请开具支持票证。 有关详细信息，请参阅[创建服务请求以联系 Office 365 支持部门](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)。 

**问：我不确定升级到较新版本的 Azure AD Connect 时的风险。您可以打电话帮我进行升级吗？**  
如果在升级到新版 Azure AD Connect 时需要帮助，请参阅[创建服务请求以联系 Office 365 支持部门](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)开具支持票证。

## <a name="troubleshooting"></a>故障排除
**问：如何获取有关 Azure AD Connect 的帮助？**

[搜索 Microsoft 知识库 (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* 在知识库 (KB) 中搜索有关 Azure AD Connect 支持的常见故障维修服务问题的技术解决方案。

[Azure Active Directory 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 转到 [Azure AD 社区](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，搜索技术问题与答案，或提出自己的问题。

[获取 Azure AD 支持](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**问：为什么在同步步骤错误后出现事件6311和6401？**

事件 6311-**服务器在执行回调时遇到意外错误**，6401-**管理代理控制器遇到意外错误**-在同步步骤出错后总是会记录该错误。 若要解决这些错误，需要清除同步步骤错误。  有关详细信息，请参阅[在同步过程中排查错误](tshoot-connect-sync-errors.md)和[排查 Azure AD Connect 同步中的对象同步](tshoot-connect-objectsync.md)

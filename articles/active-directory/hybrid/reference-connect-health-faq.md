---
title: Azure Active Directory Connect Health 常见问题-Azure |Microsoft Docs
description: 此常见问题回答有关 Azure AD Connect Health 的问题。 此常见问题涵盖有关使用该服务的问题，包括计费模式、功能、限制和支持。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e6c490ee9d8b6f7f07f52e70ceb8c7c49d699b6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897022"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health 常见问题
本文包含有关 Azure Active Directory （Azure AD）连接运行状况的常见问题（Faq）的解答。 这些 Faq 涵盖有关如何使用服务的问题，其中包括计费模式、功能、限制和支持。

## <a name="general-questions"></a>一般问题
**问：我管理多个 Azure AD 目录。如何实现切换到已 Azure Active Directory Premium 的项？**

若要在不同的 Azure AD 租户之间进行切换，请在右上角选择当前登录的**用户名**，然后选择相应的帐户。 如果此处未列出该帐户，请选择 "**注销**"，然后使用已启用 Azure Active Directory Premium 的目录的全局管理员凭据进行登录。

**问： Azure AD Connect Health 支持哪个版本的标识角色？**

下表列出了角色和受支持的操作系统版本。

|Role| 操作系统/版本|
|--|--|
|Active Directory 联合身份验证服务（AD FS）| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 版本1.0.9125 或更高版本|
|Active Directory 域服务（AD DS）| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

请注意，该服务提供的功能可能因角色和操作系统而异。 换句话说，所有操作系统版本都可能无法使用所有功能。 有关详细信息，请参阅功能说明。

**问：我需要多少个许可证来监视我的基础结构？**

* 第一个 Connect Health 代理至少需要一个 Azure AD Premium 许可证。
* 其他每个注册代理需要25个额外 Azure AD Premium 许可证。
* 代理计数等效于在所有受监视角色（AD FS、Azure AD Connect 和/或 AD DS）中注册的代理的总数。
* AAD Connect Health 授权不需要你向特定用户分配许可证。 你只需具有有效许可证的必需数目。

还可在[Azure AD 定价页](https://aka.ms/aadpricing)上找到许可信息。

示例:

| 注册的代理 | 所需许可证 | 监视配置示例 |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect 服务器 |
| 2 | 26| 1 Azure AD Connect 服务器和1个域控制器 |
| 3 | 51 | 1 Active Directory 联合身份验证服务（AD FS）服务器、1个 AD FS 代理和1个域控制器 |
| 4 | 76 | 1 AD FS server、1 AD FS 代理和2个域控制器 |
| 5 | 101 | 1 Azure AD Connect server、1 AD FS 服务器、1 AD FS 代理和2个域控制器 |

**问： Azure AD Connect Health 是否支持 Azure 德国云？**

除了 "[同步错误报告" 功能](how-to-connect-health-sync.md#object-level-synchronization-error-report)以外，德国云不支持 Azure AD Connect Health。

| 角色 | 功能 | 在德语云中受支持 |
| ------ | --------------- | --- |
| 连接运行状况以进行同步 | 监视/见解/警报/分析 | No |
|  | 同步错误报告 | 用户帐户控制 |
| 为 ADFS 连接运行状况 | 监视/见解/警报/分析 | No |
| 用于添加的连接运行状况 | 监视/见解/警报/分析 | No |

若要确保连接运行状况的代理连接以便同步，请相应地配置[安装要求](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)。

## <a name="installation-questions"></a>安装问题

**问：在单个服务器上安装 Azure AD Connect Health 代理有何影响？**

安装 Microsoft Azure AD 连接运行状况代理、AD FS、web 应用程序代理服务器、Azure AD Connect （同步）服务器、域控制器对 CPU、内存消耗、网络带宽和存储的影响很小。

以下数字是近似值：

* CPU 消耗：增加 ~ 1-5%。
* 内存消耗：最多为系统总内存的10%。

> [!NOTE]
> 如果代理无法与 Azure 通信，则代理会在本地存储数据，以获得定义的最大限制。 代理会根据 "最近的服务最少" 这一原则覆盖 "缓存" 的数据。
>
>

* Azure AD Connect Health 代理的本地缓冲区存储：约 20 MB。
* 对于 AD FS 服务器，我们建议你预配 1024 MB （1 GB）的磁盘空间，以便 Azure AD Connect Health 代理的 AD FS 审核通道处理所有审核数据，然后再将其覆盖。

**问：在 Azure AD Connect Health 代理安装期间，我是否需要重新启动我的服务器？**

不。 安装代理不需要重新启动服务器。 但是，安装某些必备步骤可能需要重新启动服务器。

例如，在 Windows Server 2008 R2 上，安装 .NET 4.5 Framework 需要重新启动服务器。

**问：是否 Azure AD Connect Health 通过传递 HTTP 代理来完成工作？**

是。 对于正在进行的操作，可以将 Health 代理配置为使用 HTTP 代理转发出站 HTTP 请求。
阅读有关[为 Health 代理配置 HTTP 代理的](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)详细信息。

如果需要在代理注册过程中配置代理，可能需要预先修改 Internet Explorer 代理设置。

1. 打开 Internet Explorer >**设置** > **internet 选项**" > **连接** > **LAN 设置**"。
2. 选择 "**为 LAN 使用代理服务器**"。
3. 如果针对 HTTP 和 HTTPS/Secure 有不同的代理端口，请选择 "**高级**"。

**问：连接到 HTTP 代理时 Azure AD Connect Health 是否支持基本身份验证？**

不。 当前不支持指定任意用户名和密码进行基本身份验证的机制。

**问：需要打开哪些防火墙端口才能让 Azure AD Connect Health 代理正常工作？**

有关防火墙端口列表和其他连接要求，请参阅 "[要求" 部分](how-to-connect-health-agent-install.md#requirements)。

**问：为什么在 Azure AD Connect Health 门户中看到两个同名的服务器？**

从服务器中删除代理时，该服务器不会自动从 Azure AD Connect Health 的门户中删除。 如果手动从服务器中删除代理或删除服务器本身，需要从 Azure AD Connect Health 门户中手动删除该服务器条目。

您可以重新映像服务器或使用相同的详细信息（例如计算机名称）创建新服务器。 如果你没有从 Azure AD Connect Health 门户中删除已注册的服务器，并且在新服务器上安装了代理，则你可能会看到两个名称相同的条目。

在这种情况下，请手动删除属于较早服务器的条目。 此服务器的数据应过时。

## <a name="health-agent-registration-and-data-freshness"></a>健康代理注册和数据刷新

**问： Health 代理注册失败的常见原因有哪些，以及如何解决问题？**

由于下列可能的原因，运行状况代理无法注册：

* 由于防火墙正在阻止流量，代理无法与所需的终结点通信。 这在 web 应用程序代理服务器上尤其常见。 请确保已允许出站通信到所需终结点和端口。 有关详细信息，请参阅 "[要求" 部分](how-to-connect-health-agent-install.md#requirements)。
* 网络层会对出站通信进行 SSL 检查。 这会导致代理使用的证书被检查服务器/实体替换，并且用于完成代理注册的步骤会失败。
* 用户没有执行代理注册的权限。 默认情况下，全局管理员具有访问权限。 您可以使用[基于角色的访问控制](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)来委托其他用户的访问权限。

**问：收到 "运行状况服务数据不是最新" 的警报。如何实现解决该问题？**

Azure AD Connect Health 在过去两小时内未接收到来自服务器的所有数据点时，将生成警报。 [阅读详细信息](how-to-connect-health-data-freshness.md)。

## <a name="operations-questions"></a>操作问题
**问：我需要在 web 应用程序代理服务器上启用审核吗？**

不需要，不需要在 web 应用程序代理服务器上启用审核。

**问：如何解决 Azure AD Connect Health 警报？**

成功情况时，Azure AD Connect Health 警报得到解决。 Azure AD Connect Health 代理会定期检测并向服务报告成功的情况。 对于一些警报，禁止显示是基于时间的。 换句话说，如果在警报生成后72小时内未观察到相同的错误条件，则警报会自动得到解决。

**问：我收到 "测试身份验证请求（综合事务）无法获取令牌" 的警报。如何实现解决该问题？**

当安装在 AD FS 服务器上的运行状况代理无法获取令牌作为运行状况代理启动的综合事务的一部分时，Azure AD Connect Health for AD FS 会生成此警报。 运行状况代理使用本地系统上下文，并尝试获取自助信赖方的令牌。 这是一个 "全部捕获" 测试，可确保 AD FS 处于颁发令牌的状态。

大多数情况下，此测试失败的原因是运行状况代理无法解析 AD FS 场名称。 如果 AD FS 服务器位于网络负载平衡器之后，并且请求是从负载平衡器后面的节点（而不是负载均衡器前面的普通客户端）启动的，则可能会发生这种情况。 为此，可以更新 "C:\Windows\System32\drivers\etc" 下的 "主机" 文件，以包含 AD FS 服务器的 IP 地址或 AD FS 场名称（如 sts.contoso.com）的环回 IP 地址（127.0.0.1）。 添加主机文件将对网络调用进行短时间线路，从而允许运行状况代理获取令牌。

**问：我收到了一封电子邮件，指出我的计算机未针对最新的勒索软件攻击进行修补。为什么我收到这封电子邮件？**

Azure AD Connect Health 服务扫描了它所监视的所有计算机，以确保安装了所需的修补程序。 如果至少有一台计算机没有关键修补程序，则会向租户管理员发送电子邮件。 以下逻辑用于做出此决定。
1. 查找计算机上安装的所有修补程序。
2. 检查定义的列表中是否存在至少一个修补程序。
3. 如果是，则计算机受保护。 否则，计算机面临攻击风险。

你可以使用以下 PowerShell 脚本手动执行此检查。 它实现上面的逻辑。

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**问：为什么 PowerShell cmdlet <i>get-msoldirsyncprovisioningerror</i>在结果中显示较少的同步错误？**

<i>Get-msoldirsyncprovisioningerror</i>将仅返回 DirSync 设置错误。 除此之外，连接运行状况门户还显示其他同步错误类型，例如导出错误。 这与 Azure AD Connect 增量结果一致。 阅读有关[Azure AD Connect 同步错误](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)的详细信息。

**问：为什么我的 ADFS 审核并未生成？**

请使用 PowerShell cmdlet <i>set-adfsproperties-AuditLevel</i>确保审核日志未处于禁用状态。 阅读有关[ADFS 审核日志](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)的详细信息。 请注意，如果已将高级审核设置推送到 ADFS 服务器，则会覆盖包含 svchost.exe 的任何更改（如果未配置应用程序，则为事件）。 在这种情况下，请将本地安全策略设置为记录应用程序生成的失败和成功。

**问：代理证书在过期之前是否自动续订？**
代理证书将在到期日期前**6 个月**自动续订。 如果未续订，请确保该代理的网络连接稳定。 重新启动代理服务或更新到最新版本也可以解决此问题。


## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [将 Azure AD Connect Health 与 AD FS 结合使用](how-to-connect-health-adfs.md)
* [使用 Azure AD Connect Health 进行同步](how-to-connect-health-sync.md)
* [将 Azure AD Connect Health 与 AD DS 结合使用](how-to-connect-health-adds.md)
* [Azure AD Connect Health 版本历史记录](reference-connect-health-version-history.md)

---
title: Azure AD Connect：先决条件和硬件 | Microsoft Docs
description: 本文介绍 Azure AD Connect 的先决条件和硬件要求。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83a05636f8e673e08bfd390aa10069da0abd561d
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542053"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect 的先决条件
本文介绍 Azure Active Directory （Azure AD）连接的先决条件和硬件要求。

## <a name="before-you-install-azure-ad-connect"></a>安装 Azure AD Connect 之前
在安装 Azure AD Connect 之前，需要准备好以下项目。

### <a name="azure-ad"></a>Azure AD
* 需要 Azure AD 租户。 通过 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)获得一个租户。 可以使用以下门户之一来管理 Azure AD Connect：
  * [Azure 门户](https://portal.azure.com)。
  * [Office 门户](https://portal.office.com)。
* [添加并验证域](../active-directory-domains-add-azure-portal.md)，该域是计划在 Azure AD 中使用的。 例如，如果计划对用户使用 contoso.com，请确保此域已经过验证，并且未仅使用 contoso.onmicrosoft.com 默认域。
* 默认情况下，Azure AD 租户允许50000对象。 验证域时，限制将增加到300000对象。 如果 Azure AD 中需要更多的对象，请打开支持案例，以便进一步增加限制。 如果需要500000个以上的对象，则需要一个许可证，如 Office 365、Azure AD Premium 或企业移动性 + 安全性。

### <a name="prepare-your-on-premises-data"></a>准备本地数据
* 使用 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) 确定目录中的错误，如重复项和格式设置问题，然后同步到 Azure AD 和 Office 365。
* 查看[可以在 Azure AD 中启用的可选同步功能](how-to-connect-syncservice-features.md)，并评估应启用的功能。

### <a name="on-premises-active-directory"></a>本地 Active Directory
* Active Directory 架构版本和林功能级别必须是 Windows Server 2003 或更高版本。 只要满足架构版本和林级别的要求，域控制器就可以运行任何版本。
* 如果计划使用*密码写回*功能，则域控制器必须在 Windows Server 2008 R2 或更高版本上。
* Azure AD 使用的域控制器必须可写。 *不支持*使用只读域控制器（RODC），Azure AD Connect 不遵循任何写重定向。
* 使用 "点线" （名称包含句点 "."）来使用本地林或域。*不支持*NetBIOS 名称。
* 建议[启用 Active Directory 回收站](how-to-connect-sync-recycle-bin.md)。

### <a name="azure-ad-connect-server"></a>Azure AD Connect 服务器
Azure AD Connect 服务器包含关键的标识数据。 确保对此服务器的管理访问权限得到适当的保护非常重要。 遵循[保护特权访问](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)中的指导原则。 

必须将 Azure AD Connect 服务器视为第0层组件，如[Active Directory 管理层模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)中所述 

若要详细了解如何保护 Active Directory 环境，请参阅[保护 Active Directory 的最佳实践](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)。

#### <a name="installation-prerequisites"></a>安装的先决条件

- Azure AD Connect 必须安装在已加入域的 Windows Server 2012 或更高版本上。 
- 在2019之前，不能在 Small Business Server 或 Windows Server Essentials 上安装 Azure AD Connect （支持 Windows Server Essentials 2019）。 该服务器必须使用 Windows Server Standard 或更高版本。 
- 必须在 Azure AD Connect 服务器上安装完整的 GUI。 不支持在 Windows Server Core 上安装 Azure AD Connect。 
- 如果使用 Azure AD Connect 向导来管理 Active Directory 联合身份验证服务（AD FS）配置，则 Azure AD Connect 服务器不能组策略启用 PowerShell 脚本。 如果使用 Azure AD Connect 向导来管理同步配置，则可以启用 PowerShell 脚本。 
- 如果正在部署 AD FS： 
    - 安装 AD FS 或 Web 应用程序代理的服务器必须是 Windows Server 2012 R2 或更高版本。 必须在这些服务器上启用 Windows 远程管理才能进行远程安装。 
    - 必须配置 TLS/SSL 证书。 有关详细信息，请参阅[管理 ssl/TLS 协议和密码套件 AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)和[管理 AD FS 中的 ssl 证书](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)。
    - 必须配置名称解析。 
- 如果全局管理员已启用 MFA，则 URL https://secure.aadcdn.microsoftonline-p.com *必须*位于 "受信任的站点" 列表中。 当系统提示你输入 MFA 质询时，系统将提示你将此站点添加到 "受信任的站点" 列表中，并且该站点尚未添加。 可以使用 Internet Explorer 将它添加到受信任站点。

#### <a name="harden-your-azure-ad-connect-server"></a>强化 Azure AD Connect 服务器 
建议你强化 Azure AD Connect 服务器，以降低 IT 环境关键组件的安全攻击面。 遵循这些建议将有助于缓解组织面临的一些安全风险。

- 将 Azure AD Connect 视为域控制器和其他第0层资源。 有关详细信息，请参阅[Active Directory 管理层模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)。
- 将 Azure AD Connect 服务器的管理访问权限仅限于域管理员或其他严格控制的安全组。
- [为具有特权访问权限的所有人员创建专用帐户](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)。 管理员不应该浏览 web、检查电子邮件，以及通过具有高特权帐户的日常工作效率任务。
- 按照[保护特权访问](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/how-to-configure-protected-accounts)中提供的指南进行操作。 
- 确保每台计算机都具有唯一的本地管理员密码。 有关详细信息，请参阅[本地管理员密码解决方案（LAPS）](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps)可在每个工作站上配置唯一的随机密码，并将其存储在受 ACL 保护 Active Directory 中。 只有符合条件的授权用户才可以读取或请求重置这些本地管理员帐户密码。 你可以从[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.)获取用于工作站和服务器的 LAPS。 [基于清洁源原则的操作标准](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)中提供了有关使用 LAPS 和特权访问工作站（paw）操作环境的其他指导。 
- 为拥有组织信息系统的特许访问权限的所有人员实现专用的[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)。 
- 遵循这些[附加指导原则](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)，降低 Active Directory 环境的受攻击面。


### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect 所使用的 SQL Server
* Azure AD Connect 要求使用 SQL Server 数据库来存储标识数据。 默认情况下，会安装 SQL Server 2012 Express LocalDB （轻型版本的 SQL Server Express）。 SQL Server Express 的大小限制为 10 GB，使你能够管理大约100000个对象。 如果需要管理更多的目录对象，请将安装向导指向不同的 SQL Server 安装。 SQL Server 安装的类型可能会影响 [Azure AD Connect 的性能](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)。
* 如果使用 SQL Server 的其他安装，则需要满足以下要求：
  * Azure AD Connect 支持 2012 SQL Server 的所有版本（具有最新 Service Pack）到 SQL Server 2019。 *不支持*将 Azure SQL 数据库用作数据库。
  * 必须使用不区分大小写的 SQL 排序规则。 可通过名称中的 \_CI_ 识别这些排序规则。 不支持使用区分大小写的排序规则 \_ CS_ 名称标识*isn't supported*。
  * 每个 SQL 实例只能有一个同步引擎。 *不支持*与 FIM/MIM Sync、DirSync 或 AZURE AD SYNC 共享 SQL 实例。

### <a name="accounts"></a>帐户
* 您必须为要与之集成的 Azure AD 租户 Azure AD 全局管理员帐户。 此帐户必须是*学校或组织帐户*，而不能是*Microsoft 帐户*。
* 如果使用[快速设置](reference-connect-accounts-permissions.md#express-settings-installation)或者从 DirSync 升级，则必须具有本地 Active Directory 的企业管理员帐户。
* 如果使用自定义设置安装路径，则可以使用更多选项。 有关详细信息，请参阅[自定义安装设置](reference-connect-accounts-permissions.md#custom-installation-settings)。

### <a name="connectivity"></a>连接
* Azure AD Connect 服务器需要 Intranet 和 Internet 的 DNS 解析。 DNS 服务器必须能够将名称解析成本地 Active Directory 以及 Azure AD 终结点。
* 如果 intranet 上有防火墙并且需要打开 Azure AD Connect 服务器与域控制器之间的端口，请参阅[Azure AD Connect 端口](reference-connect-ports.md)以获取详细信息。
* 如果代理或防火墙限制了可访问的 Url，则必须打开[Office 365 url 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中所述的 url。
  * 如果使用的是德国或 Microsoft Azure 政府云中的 Microsoft 云，请参阅 Url 的[Azure AD Connect 同步服务实例注意事项](reference-connect-instances.md)。
* Azure AD Connect（1.1.614.0 版及更高版本）默认情况下使用 TLS 1.2 对同步引擎和 Azure AD 之间的通信进行加密。 如果 TLS 1.2 在基础操作系统上不可用，Azure AD Connect 会递增地回退到较旧的协议（TLS 1.1 和 TLS 1.0）。
* 在 1.1.614.0 版以前，Azure AD Connect 默认情况下使用 TLS 1.0 对同步引擎和 Azure AD 之间的通信进行加密。 若要更改为 TLS 1.2，请按照[为 Azure AD connect 启用 TLS 1.2](#enable-tls-12-for-azure-ad-connect) 中的步骤进行操作。
* 如果正在使用出站代理连接到 internet，则必须为安装向导添加**C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config**文件中的以下设置，并且 Azure AD Connect 同步才能连接到 internet 并 Azure AD。 必须在文件底部输入此文本。 在此代码中， * &lt; PROXYADDRESS &gt; *代表实际代理 IP 地址或主机名。

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* 如果代理服务器要求身份验证，则[服务帐户](reference-connect-accounts-permissions.md#adsync-service-account)必须位于域中。 使用自定义设置安装路径来指定[自定义服务帐户](how-to-connect-install-custom.md#install-required-components)。 还需要对 machine.config 进行不同的更改。在 machine.config 中进行此更改之后，安装向导和同步引擎响应来自代理服务器的身份验证请求。 在所有安装向导页中（"**配置**" 页除外）都使用已登录用户的凭据。 在安装向导结束时的 "**配置**" 页上，上下文将切换到你创建的[服务帐户](reference-connect-accounts-permissions.md#adsync-service-account)。 machine.config 部分应如下所示：

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* 如果正在现有的安装程序中完成代理配置，则**Microsoft Azure AD 同步服务**需要重新启动一次，以便 Azure AD Connect 读取代理配置并更新 behviour。 
* 当 Azure AD Connect 在目录同步过程中将 Web 请求发送到 Azure AD 时，Azure AD 可能需要最多 5 分钟才能响应。 代理服务器具有连接空闲超时配置很常见。 确保配置设置为至少6分钟或更高。

有关详细信息，请参阅 MSDN 上的[默认代理元素](https://msdn.microsoft.com/library/kd3cf2ex.aspx)。
有关遇到连接问题时的详细信息，请参阅[排查连接问题](tshoot-connect-connectivity.md)。

### <a name="other"></a>其他
可选：使用测试用户帐户来验证同步。

## <a name="component-prerequisites"></a>组件先决条件
### <a name="powershell-and-net-framework"></a>PowerShell 和 .NET Framework
Azure AD Connect 依赖于 Microsoft PowerShell 和 .NET Framework 4.5.1。 服务器上需要安装此版本或更高版本。 根据你的 Windows Server 版本，请执行以下操作：

* Windows Server 2012 R2
  * 已按默认安装 Microsoft PowerShell， 因此不需要执行任何操作。
  * .NET Framework 4.5.1 和更高版本通过 Windows 更新提供。 请确保已在控制面板中安装 Windows Server 的最新更新。
* Windows Server 2012
  * Windows Management Framework 4.0 中提供了 Microsoft PowerShell 的最新版本，该版本可在[Microsoft 下载中心](https://www.microsoft.com/downloads)上找到。
  * [Microsoft 下载中心](https://www.microsoft.com/downloads)提供 .NET Framework 4.5.1 和更高版本。


### <a name="enable-tls-12-for-azure-ad-connect"></a>为 Azure AD connect 启用 TLS 1.2
在 1.1.614.0 版以前，Azure AD Connect 默认情况下使用 TLS 1.0 对同步引擎服务器和 Azure AD 之间的通信进行加密。 默认情况下，你可以将 .NET 应用程序配置为在服务器上使用 TLS 1.2。 有关 TLS 1.2 的详细信息，请参阅[Microsoft 安全公告 2960358](https://technet.microsoft.com/security/advisory/2960358)。

1. 请确保已为操作系统安装了 .NET 4.5.1 修补程序。 有关详细信息，请参阅[Microsoft 安全公告 2960358](https://technet.microsoft.com/security/advisory/2960358)。 服务器上可能已经安装了此修补程序或更高版本的修补程序。

1. 对于所有操作系统，设置此注册表项并重新启动服务器。
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. 如果还想要在同步引擎服务器和远程 SQL Server 之间启用 TLS 1.2，请确保已安装适用于[tls 1.2 的 Microsoft SQL Server](https://support.microsoft.com/kb/3135244)的所需版本。

## <a name="prerequisites-for-federation-installation-and-configuration"></a>联合身份验证安装和配置的先决条件
### <a name="windows-remote-management"></a>Windows 远程管理
使用 Azure AD Connect 部署 AD FS 或 Web 应用程序代理（WAP）时，请检查以下要求：

* 如果目标服务器已加入域，请确保已启用 "Windows 远程托管"。
  * 在提升权限的 PowerShell 命令窗口中，使用命令 `Enable-PSRemoting –force` 。
* 如果目标服务器是未加入域的 WAP 计算机，则需要满足以下几个附加要求：
  * 在目标计算机（WAP 计算机）上：
    * 通过 "服务" 管理单元确保 Windows 远程管理/WS-MANAGEMENT （WinRM）服务正在运行。
    * 在提升权限的 PowerShell 命令窗口中，使用命令 `Enable-PSRemoting –force` 。
  * 在运行向导的计算机上（如果目标计算机未加入域或者是不受信任的域）：
    * 在提升权限的 PowerShell 命令窗口中，使用命令 `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` 。
    * 在服务器管理器中：
      * 将 DMZ WAP 主机添加到计算机池。 在服务器管理器中，选择 "**管理**" "  >  **添加服务器**"，然后使用 " **DNS** " 选项卡。
      * 在 "**服务器管理器所有服务器**" 选项卡上，右键单击 WAP 服务器，并选择 "**管理为**"。 输入 WAP 计算机的本地（非域）凭据。
      * 若要验证远程 PowerShell 连接，请在 "**服务器管理器所有服务器**" 选项卡上，右键单击 WAP 服务器并选择 " **Windows PowerShell**"。 应打开远程 PowerShell 会话，以确保可以建立远程 PowerShell 会话。

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 证书要求
* 建议在 AD FS 场的所有节点和所有 Web 应用程序代理服务器中使用相同的 TLS/SSL 证书。
* 该证书必须是 X509 证书。
* 在测试实验室环境中，可以在联合服务器上使用自签名证书。 对于生产环境，建议从公共证书颁发机构获取证书。
  * 如果你使用的是未公开信任的证书，请确保每个 Web 应用程序代理服务器上安装的证书在本地服务器和所有联合服务器上都受信任。
* 证书的标识必须与联合身份验证服务名称（例如 sts.contoso.com）匹配。
  * 标识是类型为 dNSName 的使用者备用名称（SAN）扩展，或者，如果没有 SAN 条目，则将使用者名称指定为公用名。
  * 证书中可以存在多个 SAN 条目，其中其中一个条目与联合身份验证服务名称匹配。
  * 如果打算使用 Workplace Join，则需要使用 enterpriseregistration 值进行附加的 SAN **。** 后跟组织的用户主体名称（UPN）后缀，例如，enterpriseregistration.contoso.com。
* 不支持基于 CryptoAPI 下一代（CNG）密钥和密钥存储提供程序（Ksp）的证书。 因此，你必须使用基于加密服务提供程序（CSP）的证书，而不是 KSP。
* 支持通配符证书。

### <a name="name-resolution-for-federation-servers"></a>联合服务器的名称解析
* 为 intranet （内部 DNS 服务器）和 extranet （通过域注册机构的公共 DNS）的 AD FS 名称（例如，sts.contoso.com）设置 DNS 记录。 对于 Intranet DNS 记录，请确保使用 A 记录而不是 CNAME 记录。 Windows 身份验证需要使用记录才能从已加入域的计算机正常工作。
* 如果要部署多个 AD FS 服务器或 Web 应用程序代理服务器，请确保已配置负载均衡器，并确保 AD FS 名称（例如 sts.contoso.com）的 DNS 记录指向负载均衡器。
* 若要让 Windows 集成身份验证在 intranet 中使用 Internet Explorer 的浏览器应用程序，请确保将 AD FS 名称（例如 sts.contoso.com）添加到 Internet Explorer 中的 intranet 区域。 可以通过组策略控制此要求并将其部署到所有已加入域的计算机。

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect 支持组件
Azure AD Connect 在安装了 Azure AD Connect 的服务器上安装以下组件。 此列表针对基本快速安装。 如果在 "**安装同步服务**" 页上选择使用不同的 SQL Server，则不会在本地安装 SQL Express LocalDB。

* Azure AD Connect Health
* Microsoft SQL Server 2012 命令行实用工具
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 本机客户端
* Microsoft Visual C++ 2013 再分发包

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect 的硬件要求
下表显示 Azure AD Connect 同步计算机的最低要求。

| Active Directory 中的对象数目 | CPU | 内存 | 硬盘驱动器大小 |
| --- | --- | --- | --- |
| 少于 10,000 个 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| 对于100000或更多对象，需要 SQL Server 的完整版本 | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| 超过 600,000 个 |1.6 GHz |32 GB |500 GB |

运行 AD FS 或 Web 应用程序代理服务器的计算机的最低要求如下：

* CPU：双核 1.6 GHz 或更高
* 内存： 2 GB 或更高
* Azure VM：A2 配置或更高

## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。

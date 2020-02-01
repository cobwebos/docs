---
title: Azure AD Connect Health 代理安装 |Microsoft Docs
description: 这是描述用于 AD FS 和同步的代理安装的 Azure AD Connect Health 页面。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e9468c0a0f6844c7522ff43761cf58f4beea27e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897357"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health 代理安装

本文档将指导你完成安装和配置 Azure AD Connect Health 代理。 可从[此处](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)下载代理。

## <a name="requirements"></a>需求

下表列出了使用 Azure AD Connect Health 的要求。

| 不必 | description |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health 是一种 Azure AD Premium 功能，需要 Azure AD Premium。 <br /><br />有关详细信息，请参阅[Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)入门 <br />若要开始30天免费试用，请参阅[开始试用。](https://azure.microsoft.com/trial/get-started-active-directory/) |
| 你必须是 Azure AD 的全局管理员才能开始使用 Azure AD Connect Health |默认情况下，只有全局管理员才能安装和配置运行状况代理以开始，访问门户，并在 Azure AD Connect Health 中执行任何操作。 有关详细信息，请参阅[管理 Azure AD 目录](../fundamentals/active-directory-administer.md)。 <br /><br /> 使用基于角色的访问控制，你可以允许 Azure AD Connect Health 访问你的组织中的其他用户。 有关详细信息，请参阅[Azure AD Connect Health 的基于角色的访问控制。](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) <br /><br />**重要提示：** 安装代理时使用的帐户必须是工作帐户或学校帐户。 它不能是 Microsoft 帐户。 有关详细信息，请参阅以[组织身份注册 Azure](../fundamentals/sign-up-organization.md) |
| 每个目标服务器上都安装了 Azure AD Connect Health 代理 | Azure AD Connect Health 要求在目标服务器上安装和配置运行状况代理，以接收数据并提供监视和分析功能。 <br /><br />例如，若要从 AD FS 基础结构获取数据，必须在 AD FS 和 Web 应用程序代理服务器上安装代理。 同样，若要获取本地 AD DS 基础结构的数据，必须将代理安装在域控制器上。 <br /><br /> |
| 到 Azure 服务终结点的出站连接 | 在安装和运行时，代理需要连接到 Azure AD Connect Health 服务终结点。 如果使用防火墙阻止出站连接，请确保将以下终结点添加到允许列表中。 查看[出站连接终结点](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) |
|基于 IP 地址的出站连接 | 对于防火墙上基于 IP 地址的筛选，请参阅[AZURE IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。|
| 筛选或禁用出站流量的 SSL 检查 | 如果网络层上的出站流量存在 SSL 检查或终止，则代理注册步骤或数据上传操作可能会失败。 详细了解[如何设置 SSL 检查](https://technet.microsoft.com/library/ee796230.aspx) |
| 运行代理的服务器上的防火墙端口 |代理要求打开以下防火墙端口，以便代理能够与 Azure AD Health 服务终结点进行通信。<br /><br /><li>TCP 端口443</li><li>TCP 端口5671</li> <br />请注意，最新版本的代理不再需要端口5671。 升级到最新版本，以便只需要端口443。 阅读有关[启用防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx)的详细信息 |
| 如果启用了 IE 增强安全性，请允许以下网站 |如果启用了 "IE 增强的安全性"，则必须在要安装代理的服务器上允许以下网站。<br /><br /><li>https：\//login.microsoftonline.com</li><li>https：\//secure.aadcdn.microsoftonline-p.com</li><li>https：\//login.windows.net</li><li>https：\//aadcdn.msftauth.net</li><li>Azure Active Directory 信任的组织的联合服务器。 例如： https：\//sts.contoso.com</li> 阅读有关[如何配置 IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)的详细信息。 如果网络中有一个代理，请参阅下面的说明。|
| 确保安装了 PowerShell v4.0 或更高版本 | <li>Windows Server 2008 R2 随 PowerShell v2.0 一起提供，该版本对于代理来说是不够的。 更新 PowerShell，如下所述： [Windows Server 2008 R2 服务器上的代理安装](#agent-installation-on-windows-server-2008-r2-servers)。</li><li>Windows Server 2012 附带了 PowerShell v3.0，这对于代理来说是不够的。  [更新](https://www.microsoft.com/download/details.aspx?id=40855)Windows Management Framework。</li><li>Windows Server 2012 R2 及更高版本附带了足够的最新版本的 PowerShell。</li>|
|禁用 FIPS|Azure AD Connect Health 代理不支持 FIPS。|


> [!NOTE]
> 如果有高度锁定的环境，则需要将下面的服务终结点列表中提到的 Url 列入允许列表，以及上述 "允许的 IE 增强的安全配置" 中列出的 Url。 
>

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>到 Azure 服务终结点的出站连接

 在安装和运行时，代理需要连接到 Azure AD Connect Health 服务终结点。 如果使用防火墙阻止出站连接，请确保默认情况下不会阻止以下 Url。 不要禁用对这些 Url 的安全监视或检查，但允许它们与其他 internet 流量相同。 它们允许与 Azure AD Connect Health 服务终结点进行通信。 了解如何[通过 test-azureadconnecthealthconnectivity 检查出站连接](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#test-connectivity-to-azure-ad-connect-health-service)。

| 域环境 | 必需的 Azure 服务终结点 |
| --- | --- |
| 一般公共 | <li>&#42;。 blob.core.windows.net </li><li>&#42;。 aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-端口：5671 </li><li>&#42;。 adhybridhealth.azure.com/</li><li>https：\//management.azure.com </li><li>https：\//policykeyservice.dc.ad.msft.net/</li><li>https：\//login.windows.net</li><li>https：\//login.microsoftonline.com</li><li>https：\//secure.aadcdn.microsoftonline-p.com </li><li>https：\//www.office.com * 此终结点仅用于在注册过程中发现目的。</li> |
| Azure 德国 | <li>&#42;。 blob.core.cloudapi.de </li><li>&#42;。 servicebus.cloudapi.de </li> <li>&#42;。 aadconnecthealth.microsoftazure.de </li><li>https：\//management.microsoftazure.de </li><li>https：\//policykeyservice.aadcdi.microsoftazure.de </li><li>https：\//login.microsoftonline.de </li><li>https：\//secure.aadcdn.microsoftonline-p.de </li><li>https：\//www.office.de * 此终结点仅用于在注册过程中发现目的。</li> |
| Azure 政府 | <li>&#42;。 blob.core.usgovcloudapi.net </li> <li>&#42;。 servicebus.usgovcloudapi.net </li> <li>&#42;。 aadconnecthealth.microsoftazure.us </li> <li>https：\//management.usgovcloudapi.net </li><li>https：\//policykeyservice.aadcdi.azure.us </li><li>https：\//login.microsoftonline.us </li><li>https：\//secure.aadcdn.microsoftonline-p.com </li><li>https：\//www.office.com * 此终结点仅用于在注册过程中发现目的。</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>下载并安装 Azure AD Connect Health 代理

* 请确保满足 Azure AD Connect Health[的要求](how-to-connect-health-agent-install.md#requirements)。
* 开始使用 Azure AD Connect Health AD FS
    * [下载 AD FS Azure AD Connect Health 代理。](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [请参阅安装说明](#installing-the-azure-ad-connect-health-agent-for-ad-fs)。
* 开始使用 Azure AD Connect Health 进行同步
    * [下载并安装最新版本的 Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771)。 用于同步的运行状况代理将作为 Azure AD Connect 安装的一部分（1.0.9125.0 或更高版本）进行安装。
* 开始使用 Azure AD Connect Health AD DS
    * [下载 AD DS Azure AD Connect Health 代理](https://go.microsoft.com/fwlink/?LinkID=820540)。
    * [请参阅安装说明](#installing-the-azure-ad-connect-health-agent-for-ad-ds)。

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>为 AD FS 安装 Azure AD Connect Health 代理

> [!NOTE]
> AD FS server 应该不同于同步服务器。 不要将 AD FS 代理安装到你的同步服务器。
>

安装之前，请确保 AD FS server 主机名是唯一的，且在 AD FS 服务中不存在。
若要启动代理安装，请双击下载的 .exe 文件。 在第一个屏幕上，单击 "安装"。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install1.png)

安装完成后，单击 "立即配置"。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install2.png)

这将启动 PowerShell 窗口以启动代理注册过程。 出现提示时，请使用有权执行代理注册的 Azure AD 帐户登录。 默认情况下，全局管理员帐户具有访问权限。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install3.png)

登录后，PowerShell 将继续。 完成后，可以关闭 PowerShell 并完成配置。

此时，应自动启动代理服务，以安全的方式将所需的数据上传到云服务。

如果你尚未满足前面几节中所述的所有先决条件，则 PowerShell 窗口中会出现警告。 在安装代理之前，请务必完成这些[要求](how-to-connect-health-agent-install.md#requirements)。 以下屏幕截图是这些错误的一个示例。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install4.png)

若要验证代理是否已安装，请在服务器上查找以下服务。 如果已完成配置，它们应该已经在运行。 否则，它们将停止，直到配置完成。

* Azure AD Connect Health AD FS 诊断服务
* Azure AD Connect Health AD FS Insights 服务
* Azure AD Connect Health AD FS 监视服务

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Windows Server 2008 R2 服务器上的代理安装

Windows Server 2008 R2 服务器的步骤：

1. 确保服务器正在 Service Pack 1 或更高版本上运行。
2. 关闭用于代理安装的 IE ESC：
3. 在安装 AD Health 代理之前，在每台服务器上安装 Windows PowerShell 4.0。 若要安装 Windows PowerShell 4.0：
   * 使用以下链接安装[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) ，以下载脱机安装程序。
   * 安装 PowerShell ISE （通过 Windows 功能）
   * 安装[Windows Management Framework 4.0。](https://www.microsoft.com/download/details.aspx?id=40855)
   * 在服务器上安装 Internet Explorer 版本10或更高版本。 （使用 Azure 管理员凭据进行身份验证运行状况服务需要。）
4. 有关在 Windows Server 2008 R2 上安装 Windows PowerShell 4.0 的详细信息，请参阅[此处](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)的 wiki 文章。

### <a name="enable-auditing-for-ad-fs"></a>为 AD FS 启用审核

> [!NOTE]
> 本部分仅适用于 AD FS 服务器。 无需在 Web 应用程序代理服务器上执行这些步骤。
>

为了使使用情况分析功能收集和分析数据，Azure AD Connect Health 代理需要 AD FS 审核日志中的信息。 默认情况下未启用这些日志。 使用以下过程来启用 AD FS 审核并在 AD FS 服务器上查找 AD FS 审核日志。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>为 Windows Server 2008 R2 上的 AD FS 启用审核

1. 单击 "**开始**"，依次指向 "**程序**"、"**管理工具**"，然后单击 "**本地安全策略**"。
2. 导航到 "**安全 \ 本地策略 \ 安全策略分配**" 文件夹，然后双击 "**生成安全审核**"。
3. 在 "**本地安全设置**" 选项卡上，验证是否列出了 "AD FS 2.0" 服务帐户。 如果不存在，请单击 "**添加用户或组**" 并将其添加到列表中，然后单击 **"确定"** 。
4. 若要启用审核，请使用提升的权限打开命令提示符，并运行以下命令： <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. 关闭 "**本地安全策略**"。
<br />   -- **主 AD FS 服务器仅需执行以下步骤。** -- <br />
6. 打开 " **AD FS 管理**" 管理单元。 若要打开 "AD FS 管理" 管理单元，请单击 "**开始**"，指向 "**程序**"，指向 "**管理工具**"，然后单击 " **AD FS 2.0 管理**"。
7. 在 "**操作**" 窗格中，单击 "**编辑联合身份验证服务属性**"。
8. 在 "**联合身份验证服务属性**" 对话框中，单击 "**事件**" 选项卡。
9. 选中 "**成功审核**" 和 "**失败审核**" 复选框。
10. 单击“确定”。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>为 Windows Server 2012 R2 上的 AD FS 启用审核

1. 打开 "**本地安全策略**"，方法是在 "开始" 屏幕上打开**服务器管理器**，或在桌面上的任务栏中服务器管理器，然后单击 "**工具/本地安全策略**"。
2. 导航到 "**安全 \ 本地策略 \ 安全策略分配**" 文件夹，然后双击 "**生成安全审核**"。
3. 在 "**本地安全设置**" 选项卡上，验证是否列出了 "AD FS 服务帐户"。 如果不存在，请单击 "**添加用户或组**" 并将其添加到列表中，然后单击 **"确定"** 。
4. 若要启用审核，请使用提升的权限打开命令提示符，并运行以下命令： ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```
5. 关闭 "**本地安全策略**"。
<br />   -- **主 AD FS 服务器仅需执行以下步骤。** -- <br />
6. 打开**AD FS 管理**"管理单元（在服务器管理器中，单击" 工具 "，然后选择" AD FS 管理 "）。
7. 在 "**操作**" 窗格中，单击 "**编辑联合身份验证服务属性**"。
8. 在 "**联合身份验证服务属性**" 对话框中，单击 "**事件**" 选项卡。
9. 选中 "**成功审核" 和 "失败审核**" 复选框，然后单击 **"确定"** 。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>为 Windows Server 2016 上的 AD FS 启用审核

1. 打开 "**本地安全策略**"，方法是在 "开始" 屏幕上打开**服务器管理器**，或在桌面上的任务栏中服务器管理器，然后单击 "**工具/本地安全策略**"。
2. 导航到 "**安全 \ 本地策略 \ 安全策略分配**" 文件夹，然后双击 "**生成安全审核**"。
3. 在 "**本地安全设置**" 选项卡上，验证是否列出了 "AD FS 服务帐户"。 如果不存在，请单击 "**添加用户或组**" 并将 AD FS 服务帐户添加到列表中，然后单击 **"确定"** 。
4. 若要启用审核，请使用提升的权限打开命令提示符，并运行以下命令： <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. 关闭 "**本地安全策略**"。
<br />   -- **主 AD FS 服务器仅需执行以下步骤。** -- <br />
6. 打开**AD FS 管理**"管理单元（在服务器管理器中，单击" 工具 "，然后选择" AD FS 管理 "）。
7. 在 "**操作**" 窗格中，单击 "**编辑联合身份验证服务属性**"。
8. 在 "**联合身份验证服务属性**" 对话框中，单击 "**事件**" 选项卡。
9. 选中 "**成功审核" 和 "失败审核**" 复选框，然后单击 **"确定"** 。 默认情况下应启用此功能。
10. 打开 PowerShell 窗口并运行以下命令： ```Set-AdfsProperties -AuditLevel Verbose```。

请注意，默认情况下已启用 "基本" 审核级别。 阅读有关[Windows Server 2016 中的 AD FS 审核增强功能](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)的详细信息


#### <a name="to-locate-the-ad-fs-audit-logs"></a>查找 AD FS 审核日志

1. 打开**事件查看器**。
2. 请参阅 "Windows 日志"，然后选择 "**安全性**"。
3. 在右侧，单击 "**筛选当前日志**"。
4. 在 "事件源" 下，选择 " **AD FS 审核**"。

    和有关审核日志的快速[常见问题解答](reference-connect-health-faq.md#operations-questions)。

![AD FS 审核日志](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> 组策略可以禁用 AD FS 审核。 如果 AD FS 审核处于禁用状态，则无法使用有关登录活动的使用情况分析。 确保没有禁用 AD FS 审核的组策略。 >
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>安装 Azure AD Connect Health 代理进行同步

同步的 Azure AD Connect Health 代理会在 Azure AD Connect 的最新版本中自动安装。 若要使用 Azure AD Connect 进行同步，需要下载最新版本的 Azure AD Connect 并进行安装。 可在[此处](https://www.microsoft.com/download/details.aspx?id=47594)下载最新版本。

若要验证代理是否已安装，请在服务器上查找以下服务。 如果已完成配置，它们应该已经在运行。 否则，它们将停止，直到配置完成。

* Azure AD Connect Health 同步见解服务
* Azure AD Connect Health 同步监视服务

![验证同步 Azure AD Connect Health](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> 请记住，使用 Azure AD Connect Health 需要 Azure AD Premium。 如果没有 Azure AD Premium，则无法在 Azure 门户中完成配置。 有关详细信息，请参阅 "[要求" 页](how-to-connect-health-agent-install.md#requirements)。
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>手动 Azure AD Connect Health 进行同步注册

如果成功安装 Azure AD Connect 后，同步代理注册 Azure AD Connect Health 失败，可以使用以下 PowerShell 命令手动注册代理。

> [!IMPORTANT]
> 仅当在安装 Azure AD Connect 后代理注册失败时，才需要使用此 PowerShell 命令。
>
>

仅当运行状况代理注册失败（即使在成功安装和配置 Azure AD Connect 之后），以下 PowerShell 命令才是必需的。 成功注册代理后，将启动 Azure AD Connect Health 服务。

你可以使用以下 PowerShell 命令手动注册用于同步的 Azure AD Connect Health 代理：

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

此命令采用以下参数：

* AttributeFiltering： $true （默认值）-如果 Azure AD Connect 未同步默认属性集，且已自定义为使用筛选的属性集。 否则 $false。
* StagingMode： $false （默认值）-如果 Azure AD Connect 服务器未处于暂存模式，则 $true 如果服务器配置为处于暂存模式，则为。

当系统提示进行身份验证时，应使用用于配置 Azure AD Connect 的同一全局管理员帐户（例如 admin@domain.onmicrosoft.com）。

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>为 AD DS 安装 Azure AD Connect Health 代理

若要启动代理安装，请双击下载的 .exe 文件。 在第一个屏幕上，单击 "安装"。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

安装完成后，单击 "立即配置"。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

将启动命令提示符，后跟一些执行 Register-azureadconnecthealthaddsagent 的 PowerShell。 当系统提示登录到 Azure 时，请继续登录。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

登录后，PowerShell 将继续。 完成后，可以关闭 PowerShell 并完成配置。

此时，应自动启动服务，允许代理监视和收集数据。 如果你尚未满足前面几节中所述的所有先决条件，则 PowerShell 窗口中会出现警告。 在安装代理之前，请务必完成这些[要求](how-to-connect-health-agent-install.md#requirements)。 以下屏幕截图是这些错误的一个示例。

![验证 AD DS 的 Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

若要验证代理是否已安装，请在域控制器上查找以下服务。

* Azure AD Connect Health AD DS Insights 服务
* Azure AD Connect Health AD DS 监视服务

如果已完成配置，这些服务应已在运行。 否则，它们将停止，直到配置完成。

![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>在多个服务器中安装快速代理

1. 使用密码在 Azure AD 中创建用户帐户。
2. 通过门户为 Azure AD Connect Health 中的本地 AAD 帐户分配**所有者**角色。 请按照[此处](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)的步骤操作。 将角色分配给所有服务实例。 
3. 在本地域控制器中下载 .exe MSI 文件以进行安装。
4. 运行以下脚本以进行注册。 将参数替换为创建的新用户帐户及其密码。 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -UserPrincipalName $USERNAME -Credential $myCreds

```

1. 完成后，可以通过执行下列一项或多项操作来删除本地帐户的访问权限： 
    * 删除 AAD Connect Health 的本地帐户的角色分配
    * 旋转本地帐户的密码。 
    * 禁用 AAD 本地帐户
    * 删除 AAD 本地帐户  

## <a name="agent-registration-using-powershell"></a>使用 PowerShell 注册代理

安装适当的代理 setup.exe 后，可以根据角色使用以下 PowerShell 命令执行代理注册步骤。 打开 PowerShell 窗口并执行相应的命令：

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

这些命令接受 "Credential" 作为参数，以非交互方式或在服务器核心计算机上完成注册。
* 可以在作为参数传递的 PowerShell 变量中捕获凭据。
* 可以提供有权注册代理但未启用 MFA 的任何 Azure AD 身份。
* 默认情况下，全局管理员具有执行代理注册的权限。 还可以允许其他更少权限的标识执行此步骤。 阅读有关[基于角色的访问控制](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)的详细信息。

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>将 Azure AD Connect Health 代理配置为使用 HTTP 代理

你可以配置 Azure AD Connect Health 代理来使用 HTTP 代理。

> [!NOTE]
> * 不支持使用 "Netsh WinHttp set ProxyServerAddress"，因为代理使用 System.Net 发出 web 请求，而不是 Microsoft Windows HTTP 服务。
> * 配置的 Http 代理地址用于传递加密的 Https 消息。
> * 不支持经过身份验证的代理（使用 HTTPBasic）。
>
>

### <a name="change-health-agent-proxy-configuration"></a>更改运行状况代理的代理配置

你可以使用以下选项将 Azure AD Connect Health 代理配置为使用 HTTP 代理。

> [!NOTE]
> 必须重新启动所有 Azure AD Connect Health 代理服务才能更新代理设置。 运行下面的命令：<br />
> Restart-Service AzureADConnectHealth *
>
>

#### <a name="import-existing-proxy-settings"></a>导入现有的代理设置

##### <a name="import-from-internet-explorer"></a>从 Internet Explorer 导入

可以导入 Internet Explorer HTTP 代理设置，以便 Azure AD Connect Health 代理使用。 在运行 Health 代理的每台服务器上，执行以下 PowerShell 命令：

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>从 WinHTTP 导入

可以导入 WinHTTP 代理设置以供 Azure AD Connect Health 代理使用。 在运行 Health 代理的每台服务器上，执行以下 PowerShell 命令：

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>手动指定代理地址

通过执行以下 PowerShell 命令，你可以在运行 Health 代理的每台服务器上手动指定代理服务器：

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

示例： *set-azureadconnecthealthproxysettings-HttpsProxyAddress myproxyserver： 443*

* "地址" 可以是 DNS 可解析的服务器名称或 IPv4 地址
* 可以省略 "端口"。 如果省略，则选择443作为默认端口。

#### <a name="clear-existing-proxy-configuration"></a>清除现有的代理配置

可以通过运行以下命令来清除现有的代理配置：

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>读取当前的代理设置

可以通过运行以下命令来读取当前配置的代理设置：

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>测试与 Azure AD Connect Health 服务的连接

可能会出现问题，导致 Azure AD Connect Health 代理失去与 Azure AD Connect Health 服务的连接。 其中包括网络问题、权限问题或各种其他原因。

如果代理无法将数据发送到 Azure AD Connect Health 服务的时间超过两个小时，则会在门户中用以下警报指示： "运行状况服务数据不是最新的"。 可以通过运行以下 PowerShell 命令，确认受影响的 Azure AD Connect Health 代理是否能够将数据上载到 Azure AD Connect Health 服务：

    Test-AzureADConnectHealthConnectivity -Role ADFS

Role 参数当前采用以下值：

* ADFS
* 同步
* 相加

> [!NOTE]
> 若要使用连接工具，必须先完成代理注册。 如果无法完成代理注册，请确保满足 Azure AD Connect Health 的所有[要求](how-to-connect-health-agent-install.md#requirements)。 默认情况下，在代理注册过程中会执行此连接测试。
>
>

## <a name="related-links"></a>相关链接

* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [将 Azure AD Connect Health 与 AD FS 结合使用](how-to-connect-health-adfs.md)
* [使用 Azure AD Connect Health 进行同步](how-to-connect-health-sync.md)
* [将 Azure AD Connect Health 与 AD DS 结合使用](how-to-connect-health-adds.md)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](reference-connect-health-version-history.md)

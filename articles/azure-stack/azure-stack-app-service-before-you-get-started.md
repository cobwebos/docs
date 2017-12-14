---
title: "在部署 Azure 堆栈上的 App Service 之前 |Microsoft 文档"
description: "若要完成部署 Azure 堆栈上的应用程序服务之前的步骤"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: d6962bf2ffbf731a4aa301e663c7c7d3428080d4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>之前开始使用 Azure 堆栈上的 App Service

Azure 堆栈上的 azure 应用程序服务具有大量的系统必备必须在部署之前完成的步骤：

- 下载 Azure 堆栈帮助程序脚本上的 Azure 应用程序服务
- 高可用性
- 所需的 Azure 堆栈上的 Azure App Service 证书
- 准备文件服务器
- 准备 SQL Server
- 创建 Azure Active Directory 应用程序
- 创建 Active Directory 联合身份验证服务应用程序

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>下载 Azure 堆栈安装程序和帮助器脚本上的 Azure 应用程序服务

1. 下载[App Service 上 Azure 堆栈部署帮助程序脚本](https://aka.ms/appsvconmashelpers)。
2. 下载[Azure 堆栈安装程序上的 App Service](https://aka.ms/appsvconmasinstaller)。
3. 从帮助程序脚本 zip 文件中提取文件。 会显示以下文件和文件夹结构：
  - Common.ps1
  - 创建 AADIdentityApp.ps1
  - 创建 ADFSIdentityApp.ps1
  - 创建 AppServiceCerts.ps1
  - Get AzureStackRootCert.ps1
  - 删除 AppService.ps1
  - 模块
    - GraphAPI.psm1
    
## <a name="high-availability"></a>高可用性

Azure 堆栈上的 azure App Service 不是当前能够提供高可用性，因为 Azure 堆栈仅将部署的工作负载分配到一个单个的容错域。

若要准备 Azure 堆栈上的 Azure App Service，以实现高可用性，请务必在高度可用的配置中部署所需的文件服务器和 SQL Server。 当 Azure 堆栈支持多个容错域时，我们将提供有关如何在高度可用的配置中启用 Azure 堆栈上的 Azure App Service 的指南。


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>所需的 Azure 堆栈上的 Azure App Service 证书

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>所需的 Azure 堆栈开发工具包证书

此第一个脚本适用于 Azure 堆栈证书颁发机构来创建所需的应用程序服务的四个证书。

| 文件名 | 用途 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | 应用程序服务默认 SSL 证书 |
| Api.appservice.local.azurestack.external.pfx | App Service API SSL 证书 |
| ftp.appservice.local.azurestack.external.pfx | App Service 发布服务器 SSL 证书 |
| Sso.appservice.local.azurestack.external.pfx | 应用程序服务标识应用程序证书 |

在 Azure 堆栈开发工具包主机上运行脚本，并确保你作为 azurestack\CloudAdmin 运行 PowerShell。

1. 在 PowerShell 会话中作为 azurestack\AzureStackAdmin 运行，从你在哪里提取的帮助程序脚本的文件夹中运行创建 AppServiceCerts.ps1 脚本。 脚本在 App Service 需要创建证书脚本所在的文件夹中创建四个证书。
2. 输入密码来保护.pfx 文件，然后记下它。 必须输入在 App Service 上 Azure 堆栈安装程序。

#### <a name="create-appservicecertsps1-parameters"></a>创建 AppServiceCerts.ps1 参数

| 参数 | 必需/可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| PfxPassword | 必选 | Null | 用于保护证书的私钥密码 |
| DomainName | 必选 | local.azurestack.external | Azure 堆栈区域和域后缀 |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Azure 堆栈上的 Azure App Service 的生产部署所需证书

若要运行在生产环境中的资源提供程序，必须提供以下四个证书：

#### <a name="default-domain-certificate"></a>默认域证书

默认域证书放置在前端角色。 它是用户应用程序用来为 Azure App Service 的通配或默认域请求。 证书还用于源代码管理操作 (KUDU)。

证书必须是.pfx 格式，并且应为双使用者通配证书。 这样的默认域和 scm 终结点一个证书，即可涵盖源代码管理操作。

| 格式 | 示例 |
| --- | --- |
| \*.appservice。\<区域\>。\<DomainName\>。\<扩展\> | \*。 appservice.redmond.azurestack.external |
| \*。 scm.appservice。<region>.<DomainName>.<extension> | \*。 appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 证书

API 证书放置在管理角色和资源提供程序使用来保护 api 调用。 用于发布的证书必须包含一个与 API DNS 条目匹配的使用者：

| 格式 | 示例 |
| --- | --- |
| api.appservice。\<区域\>。\<DomainName\>。\<扩展\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>发布证书

上载内容时，发布服务器角色的证书为应用程序所有者保护 FTPS 流量。  用于发布的证书需要包含一个与 FTPS DNS 条目匹配的使用者。

| 格式 | 示例 |
| --- | --- |
| ftp.appservice。\<区域\>。\<DomainName\>。\<扩展\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>标识证书

标识应用程序的证书能够：
- AAD/ADFS directory、 Azure 堆栈和 App Service，以支持与计算资源提供程序的集成之间的集成
- 单一登录 Azure 堆栈上的 Azure App Service 中的高级开发人员工具的方案。
标识的证书必须包含一个匹配的以下格式的使用者：

| 格式 | 示例 |
| --- | --- |
| sso.appservice。\<区域\>。\<DomainName\>。\<扩展\> | sso.appservice.redmond.azurestack.external |

### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>提取 Azure 堆栈 Azure 资源管理器根证书

在 PowerShell 会话中作为 azurestack\CloudAdmin 运行，从你在哪里提取的帮助程序脚本的文件夹中运行 Get AzureStackRootCert.ps1 脚本。 脚本在 App Service 需要创建证书脚本所在的文件夹中创建四个证书。

| Get AzureStackRootCert.ps1 参数 | 必需/可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| PrivelegedEndpoint | 必选 | AzS ERCS01 | 特权终结点。 |
| CloudAdminCredential | 必选 | AzureStack\CloudAdmin | Azure 堆栈云管理员的域帐户凭据 |


## <a name="prepare-the-file-server"></a>准备文件服务器

Azure 应用程序服务要求使用文件服务器。 对于生产部署，文件服务器必须配置为高度可用并且能够处理故障。

与仅 Azure 堆栈开发工具包部署配合使用，你可以使用此示例 Azure 资源管理器部署模板部署配置的单个节点文件服务器： https://aka.ms/appsvconmasdkfstemplate。 单个节点文件服务器将在工作组中。

### <a name="provision-groups-and-accounts-in-active-directory"></a>配置组和 Active Directory 中的帐户


1. 创建以下 Active Directory 全局安全组：
    - FileShareOwners
    - FileShareUsers
2. 创建以下 Active Directory 帐户作为服务帐户：
    - FileShareOwner
    - FileShareUser

    作为安全最佳实践，这些帐户 （和所有 Web 角色） 的用户应彼此不同，并具有强用户名和密码。
    设置以下条件的密码：
     - 启用**密码永不过期**。
     - 启用**用户不能更改密码**。
     - 禁用**用户必须更改密码下次登录时须**。
3. 将帐户添加到的组成员身份，如下所示：
    - 添加**FileShareOwner**到**FileShareOwners**组。
    - 添加**FileShareUser**到**FileShareUsers**组。

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>配置组和工作组中的帐户

>[!NOTE]
> 在管理员命令提示符会话中配置文件服务器时，请执行所有以下命令。  **不要使用 PowerShell。**

当使用上面的 Azure 资源管理器模板，已经创建了用户。

1. 运行以下命令以创建 FileShareOwner 和 FileShareUser 帐户。 替换<password>与你自己的值。
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. 设置为永不过期通过运行以下 WMIC 命令的帐户的密码：
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. 创建本地组 FileShareUsers 和 FileShareOwners，并向其中添加第一步中的帐户。
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>设置内容共享

内容共享包含租户网站内容。 设置一个文件服务器上的内容共享的过程是 Active Directory 和 Workgroup 环境相同，但在 Active Directory 中的故障转移群集不同。

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>设置一个文件服务器上的内容共享 (AD 或工作组)

在单个文件服务器上，在提升的命令提示符运行以下命令。 将值为 C:\WebSites 替换为你的环境中的相应路径上。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>若要启用 WinRM，请将 FileShareOwners 组添加到本地管理员组

为了使 Windows 远程管理正常工作，你必须将 FileShareOwners 组添加到本地管理员组。

#### <a name="active-directory"></a>Active Directory

文件服务器上或每个文件服务器故障转移群集节点上，请在提升的命令提示符中运行以下命令。 值替换为<DOMAIN>你想要使用的域名。

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>工作组

文件服务器上的提升的命令提示符下运行以下命令。

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>配置对共享的访问控制

文件服务器上或在文件服务器故障转移群集节点上，这是当前的群集资源所有者，请在提升的命令提示符中运行以下命令。 将以斜体显示的值替换为你的环境特定的值。

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>工作组
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>准备 SQL Server

为 Azure 堆栈承载和计数的数据库上的 Azure App Service，你必须准备 SQL Server 以容纳 Azure 应用程序服务数据库。

对于使用 Azure 堆栈开发工具包的使用，你可以使用 SQL Express 2014 SP2 或更高版本。

对于生产和高可用性的目的，你应使用完整版本的 SQL 2014 SP2 或更高版本，启用混合模式身份验证，并将部署中的[高度可用的配置](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)。

Azure 堆栈 SQL 服务器上的 Azure 应用程序服务必须可从 App Service 的所有角色访问。 SQL Server 可以部署在 Azure 堆栈中的默认提供程序订阅。 也可以使用你的组织中的现有基础结构 （只要没有连接到 Azure 堆栈）。 如果你使用的 Azure 应用商店映像，请记住相应地配置防火墙。 

对于任何 SQL Server 角色，你可以使用默认实例或命名的实例。 但是，如果你使用的命名的实例，则务必手动启动 SQL Browser 服务然后打开端口 1434年。

## <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

配置可支持以下 Azure AD 服务主体：
- 虚拟机规模集在辅助角色层上的集成。
- Azure 函数门户和高级开发人员工具的 SSO。

这些步骤适用于 Azure AD 保护仅限 Azure 堆栈环境。

管理员必须配置为 SSO:
- 启用应用程序服务 (Kudu) 中的高级开发人员工具。
- 启用了 Azure 函数门户体验的使用。

执行以下步骤:

1. 以 azurestack\AzureStackAdmin 打开的 PowerShell 实例。
2. 转到下载并提取中的脚本的位置[先决条件步骤](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)。
3. [安装 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。
4. 运行**创建 AADIdentityApp.ps1**脚本。 当系统提示输入你的 Azure AD 租户 ID 时，输入将用于你的 Azure 堆栈部署，例如，myazurestack.onmicrosoft.com 的 Azure AD 租户 ID。
5. 在**凭据**窗口中，输入你的 Azure AD 服务管理员帐户和密码。 单击 **“确定”**。
6. 输入的证书文件路径和证书密码[前面创建的证书](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)。 默认情况下的此步骤是创建的证书**sso.appservice.local.azurestack.external.pfx**。
7. 该脚本在 Azure AD 租户中创建新的应用程序。 记下 PowerShell 输出中返回应用程序 ID。 你需要在安装过程中的此信息。
8. 打开一个新的浏览器窗口，并以登录到 Azure 门户 (portal.azure.com) **Azure Active Directory 服务管理员**。
9. 打开 Azure AD 资源提供程序。
10. 单击**应用程序注册**。
11. 搜索**应用程序 ID**作为步骤 7 的一部分返回。 列出的应用程序服务应用程序。
12. 单击**应用程序**列表中
13. 单击**所需的权限** > **授予权限** > **是**。

| 创建 AADIdentityApp.ps1 参数 | 必需/可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| DirectoryTenantName | 必选 | Null | Azure AD 租户 ID。 提供 GUID 或字符串，例如 myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | 必选 | Null | 管理 Azure 资源管理器终结点，例如 adminmanagement.local.azurestack.external |
| TenantARMEndpoint | 必选 | Null | 租户 Azure 资源管理器终结点，例如： management.local.azurestack.external |
| AzureStackAdminCredential | 必选 | Null | Azure AD 服务管理员凭据 |
| CertificateFilePath | 必选 | Null | 更早版本生成的标识应用程序证书文件路径。 |
| CertificatePassword | 必选 | Null | 用于保护证书的私钥的密码。 |

## <a name="create-an-active-directory-federation-services-application"></a>创建 Active Directory 联合身份验证服务应用程序

对于 Azure 堆栈 AD FS 保护的环境，你必须配置才能支持以下 AD FS 服务主体：
- 虚拟机规模集在辅助角色层上的集成。
- Azure 函数门户和高级开发人员工具的 SSO。

管理员需要配置为 SSO:
- 在辅助角色层上配置虚拟机缩放集集成服务主体。
- 启用应用程序服务 (Kudu) 中的高级开发人员工具。
- 启用了 Azure 函数门户体验的使用。

执行以下步骤:

1. 以 azurestack\azurestackadmin 打开的 PowerShell 实例。
2. 转到下载并提取中的脚本的位置[先决条件步骤](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)。
3. [安装 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。
4.  运行**创建 ADFSIdentityApp.ps1**脚本。
5.  在**凭据**窗口中，输入你的 AD FS 云管理帐户和密码。 单击 **“确定”**。
6.  提供的证书文件路径和证书密码[前面创建的证书](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)。 默认情况下创建此步骤的证书是 sso.appservice.local.azurestack.external.pfx。

| 创建 ADFSIdentityApp.ps1 参数 | 必需/可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 必选 | Null | 管理 Azure 资源管理器终结点。 例如，adminmanagement.local.azurestack.external。 |
| PrivilegedEndpoint | 必选 | Null | 特权终结点。 例如，AzS ERCS01。 |
| CloudAdminCredential | 必选 | Null | Azure 堆栈 cloudadmin 的域帐户凭据。 例如，Azurestack\CloudAdmin。 |
| CertificateFilePath | 必选 | Null | 标识应用程序的证书 PFX 文件的路径。 |
| CertificatePassword | 必选 | Null | 用于保护证书的私钥的密码。 |


## <a name="next-steps"></a>后续步骤

[安装 App Service 资源提供程序](azure-stack-app-service-deploy.md)。

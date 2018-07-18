---
title: 在 Azure Stack 上部署应用服务之前 | Microsoft Docs
description: 在 Azure Stack 上部署应用服务之前需要完成的步骤
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: anwestg
ms.openlocfilehash: 22901374988f6654bc1fb282315db81bb17c815f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857859"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>在 Azure Stack 上开始使用应用服务之前

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在部署 Azure Stack 上的 Azure 应用服务之前，必须完成这篇文章中的先决条件步骤。

> [!IMPORTANT]
> 将 1804年更新应用于 Azure Stack 集成系统，或在部署 Azure 应用服务 1.2 之前部署最新 Azure Stack 开发工具包 (ASDK)。

## <a name="download-the-installer-and-helper-scripts"></a>下载安装程序与帮助器脚本

1. 下载 [Azure Stack 上的应用服务部署帮助器脚本](https://aka.ms/appsvconmashelpers)。
2. 下载 [Azure Stack 上的应用服务安装程序](https://aka.ms/appsvconmasinstaller)。
3. 提取帮助器脚本 .zip 文件中的文件。 提取以下文件和文件夹：

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - 模块文件夹
     - GraphAPI.psm1

## <a name="high-availability"></a>高可用性

Azure Stack 1802 更新添加了支持容错域。 Azure Stack 上的 Azure 应用服务的新部署将分布在容错域，并提供容错能力。

现有的 Azure Stack 上的 Azure 应用服务部署已部署在 1802年更新之前，请参阅[跨容错域重新均衡应用服务资源提供程序](azure-stack-app-service-fault-domain-update.md)一文。

此外，部署所需的文件服务器和高度可用配置中的 SQL Server 实例。

## <a name="get-certificates"></a>获取证书

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Stack 的 Azure 资源管理器根证书

打开提升的 PowerShell 会话可以访问 Azure Stack 集成系统或 Azure Stack 开发工具包主机上的特权终结点的计算机上。

运行*Get-AzureStackRootCert.ps1*从帮助器脚本解压缩的文件夹的脚本。 此脚本在应用服务所需的、用于创建证书的脚本所在的同一文件夹中创建一个根证书。

运行以下 PowerShell 命令时必须提供 AzureStack\CloudAdmin 特权终结点和凭据。

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 需要 | AzS-ERCS01 | 特权终结点 |
| CloudAdminCredential | 需要 | AzureStack\CloudAdmin | Azure Stack 云管理的域帐户凭据 |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>所需的 ASDK 部署 Azure 应用服务证书

*Create-AppServiceCerts.ps1*脚本适用于 Azure Stack 证书颁发机构来创建应用服务所需的四个证书。

| 文件名 | 用途 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | 应用服务默认 SSL 证书 |
| api.appservice.local.azurestack.external.pfx | 应用服务 API SSL 证书 |
| ftp.appservice.local.azurestack.external.pfx | 应用服务发布者 SSL 证书 |
| sso.appservice.local.azurestack.external.pfx | 应用服务标识应用程序证书 |

若要创建证书，请执行以下步骤：

1. 登录到 Azure Stack 开发工具包主机使用 azurestack\azurestackadmin 身份帐户。
2. 打开提升的 PowerShell 会话。
3. 运行*Create-AppServiceCerts.ps1*从帮助器脚本解压缩的文件夹的脚本。 此脚本在应用服务用于创建证书所需的脚本所在的同一文件夹中创建四个证书。
4. 输入密码来保护 .pfx 文件，并记下该密码。 您必须输入在 Azure Stack 安装程序上的应用服务中。

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| pfxPassword | 需要 | Null | 帮助保护证书私钥的密码 |
| DomainName | 需要 | local.azurestack.external | Azure Stack 区域和域后缀 |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>所需的 Azure Stack 生产部署中的 Azure 应用服务证书

若要在生产环境中运行的资源提供程序，必须提供以下证书：

- 默认域证书
- API 证书
- 发布证书
- 标识证书

#### <a name="default-domain-certificate"></a>默认域证书

默认域证书放在“前端”角色上。 通配符或默认域请求到 Azure 应用服务的用户应用程序使用此证书。 该证书还用于源代码管理操作 (Kudu)。

该证书必须采用 .pfx 格式，并且应该是包含三个使用者的通配符证书。 此要求允许一个证书同时涵盖用于源代码管理操作的默认域和 SCM 终结点。

| 格式 | 示例 |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 证书

API 证书放在“管理”角色上。 资源提供程序使用它来帮助保护 API 调用。 用于发布的证书必须包含匹配 API DNS 条目的使用者。

| 格式 | 示例 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>发布证书

“发布者”角色的证书在应用程序所有者上传内容时保护其 FTPS 流量。 用于发布的证书必须包含匹配 FTPS DNS 条目的使用者。

| 格式 | 示例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>标识证书

标识应用程序的证书可以实现：

- Azure Active Directory (Azure AD) 或 Active Directory 联合身份身份验证服务 (AD FS) 目录、Azure Stack 与应用服务 之间的集成，以支持与计算资源提供程序的集成。
- Azure Stack 上的 Azure 应用服务中的高级开发人员工具的单一登录方案。

用于标识的证书必须包含匹配以下格式的使用者。

| 格式 | 示例 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>虚拟网络

Azure Stack 上的 azure 应用服务，可将资源提供程序部署到现有的虚拟网络，或可以作为部署的一部分创建的虚拟网络。 使用现有的虚拟网络启用内部 Ip 连接到的文件服务器和 SQL server 所需的 Azure Stack 上的 Azure 应用服务的使用。 在 Azure Stack 上安装 Azure 应用服务之前，必须使用以下地址范围和子网配置的虚拟网络：

虚拟网络 - /16

子网

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>准备文件服务器

Azure 应用服务需要使用文件服务器。 在生产部署中，必须将文件服务器配置为高度可用，且能够应对故障。

如果只部署 Azure Stack 开发工具包，则可以使用[示例 Azure 资源管理器部署模板](https://aka.ms/appsvconmasdkfstemplate)来部署已配置的单节点文件服务器。 单节点文件服务器位于工作组中。

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将文件服务器部署到独立于应用服务的子网中。

### <a name="provision-groups-and-accounts-in-active-directory"></a>在 Active Directory 中预配组和帐户

1. 创建以下 Active Directory 全局安全组：

   - FileShareOwners
   - FileShareUsers

2. 创建以下 Active Directory 帐户作为服务帐户：

   - FileShareOwner
   - FileShareUser

   作为安全性最佳做法是，这些帐户 （以及所有 web 角色） 的用户应该是唯一的并使用强用户名和密码。 根据以下条件设置密码：

   - 启用“密码永不过期”。
   - 启用“用户不能更改密码”。
   - 禁用“用户在下次登录时必须更改密码”。

3. 如下所述将帐户添加到组成员身份：

   - 将 **FileShareOwner** 添加到 **FileShareOwners** 组。
   - 将 **FileShareUser** 添加到 **FileShareUsers** 组。

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>在工作组中预配组和帐户

>[!NOTE]
> 当配置文件服务器上，运行中的以下命令**管理员命令提示符**。 <br>***不要使用 PowerShell。***

使用 Azure 资源管理器模板时已创建用户。

1. 运行以下命令创建 FileShareOwner 和 FileShareUser 帐户。 将 `<password>` 替换为自己的值。

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. 运行以下 WMIC 命令，将帐户密码设为永不过期：

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. 创建本地组 FileShareUsers 和 FileShareOwners，并将第一个步骤中创建的帐户添加到其中：

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

### <a name="provision-the-content-share"></a>预配内容共享

内容共享包含租户网站内容。 在单个文件服务器上预配内容共享的过程与在 Active Directory 和工作组环境中相同。 但是对于 Active Directory 中的故障转移群集则不同。

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>在单个文件服务器上（Active Directory 或工作组）预配内容共享

在单个文件服务器上，在权限提升的命令提示符下运行以下命令。 将 `C:\WebSites` 的值替换为环境中的相应路径。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>将 FileShareOwners 组添加到本地管理员组

为了让 Windows 远程管理正常运行，必须将 FileShareOwners 组添加到本地管理员组。

#### <a name="active-directory"></a>Active Directory

在文件服务器上或充当故障转移群集节点的每个文件服务器上，在权限提升的命令提示符下运行以下命令。 将 `<DOMAIN>` 的值替换为要使用的域名。

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>工作组

在文件服务器上，在权限提升的命令提示符下运行以下命令：

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>配置共享访问控制

在文件服务器上或故障转移群集节点（当前的群集资源所有者）上，在权限提升的命令提示符下运行以下命令。 将斜体显示的值替换为环境特定的值。

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

## <a name="prepare-the-sql-server-instance"></a>准备 SQL Server 实例

对于 Azure Stack 上的 Azure 应用服务托管和计量数据库，必须准备 SQL Server 实例，用于存放应用服务数据库。

对于 Azure Stack 开发工具包部署，可以使用 SQL Server Express 2014 SP2 或更高版本。

对于生产和高可用性目的，应使用完整版本的 SQL Server 2014 SP2 或更高版本，启用混合模式身份验证，并在[高可用性配置](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)中部署。

必须能够从所有“应用服务”角色访问 Azure Stack 上的 Azure 应用服务的 SQL Server 实例。 可以在 Azure Stack 中的默认提供程序订阅中部署 SQL Server。 或者，可以使用组织中现有的基础结构（前提是与 Azure Stack 建立了连接）。 如果使用 Azure 市场映像，请记得相应地配置防火墙。

>[!NOTE]
> 可通过市场管理功能获取许多 SQL IaaS 虚拟机映像。 在使用市场项部署 VM 之前，请确保下载最新版本的 SQL IaaS 扩展。 SQL 映像与 Azure 中提供的 SQL VM 相同。 对于从这些映像创建的 SQL VM，IaaS 扩展和相应的门户增强功能可提供自动修补和备份等功能。
>
对于任何 SQL Server 角色，可以使用默认实例或命名实例。 如果使用命名实例，请务必手动启动 SQL Server Browser 服务并打开端口 1434。

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将 SQL Server 部署到独立于应用服务和文件服务器的子网中。
>

## <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

配置 Azure AD 服务主体以支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具的 SSO。

这些步骤只适用于 Azure AD 保护的 Azure Stack 环境。

管理员必须配置 SSO 才能执行以下操作：

- 在 应用服务 (Kudu) 中启用高级开发人员工具。
- 启用 Azure Functions 门户体验。

执行以下步骤:

1. 以 azurestack\AzureStackAdmin 身份打开 PowerShell 实例。
2. 转到在[先决条件步骤](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)中下载并提取的脚本所在的位置。
3. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
4. 运行 **Create-AADIdentityApp.ps1** 脚本。 根据提示输入部署 Azure Stack 时使用的 Azure AD 租户 ID。 例如，输入**myazurestack.onmicrosoft.com**。
5. 在“凭据”窗口中，输入 Azure AD 服务管理帐户和密码。 选择“确定”。
6. 输入[前面创建的证书](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)的证书文件路径和证书密码。 默认情况下值，为此步骤创建的证书是 **sso.appservice.local.azurestack.external.pfx**。
7. 此脚本在租户 Azure AD 实例中创建新的应用程序。 请记下 PowerShell 输出中返回的应用程序 ID。 安装期间需要此信息。
8. 打开新的浏览器窗口，以 Azure Active Directory 服务管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
9. 打开 Azure AD 资源提供程序。
10. 选择“应用注册”。
11. 搜索步骤 7 返回的应用程序 ID。 随即会列出应用服务应用程序。
12. 在列表中选择“应用程序”。
13. 选择“设置”。
14. 选择“所需的权限” > “授予权限” > “是”。

```PowerShell
    Create-AADIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| DirectoryTenantName | 需要 | Null | Azure AD 租户 ID。 提供 GUID 或字符串。 例如，myazureaaddirectory.onmicrosoft.com。 |
| AdminArmEndpoint | 需要 | Null | Azure 资源管理器管理终结点。 例如 adminmanagement.local.azurestack.external。 |
| TenantARMEndpoint | 需要 | Null | Azure 资源管理器租户终结点。 例如 management.local.azurestack.external。 |
| AzureStackAdminCredential | 需要 | Null | Azure AD 服务管理员凭据。 |
| CertificateFilePath | 需要 | Null | 前面生成的标识应用程序证书文件的**完整路径**。 |
| CertificatePassword | 需要 | Null | 帮助保护证书私钥的密码。 |

## <a name="create-an-active-directory-federation-services-application"></a>创建 Active Directory 联合身份验证服务应用程序

对于受 AD FS 保护的 Azure Stack 环境，必须配置 AD FS 服务主体以支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具的 SSO。

管理员必须配置 SSO 才能执行以下操作：

- 针对辅助角色层上的虚拟机规模集集成配置服务主体。
- 在 应用服务 (Kudu) 中启用高级开发人员工具。
- 启用 Azure Functions 门户体验。

执行以下步骤:

1. 以 azurestack\AzureStackAdmin 身份打开 PowerShell 实例。
2. 转到在[先决条件步骤](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)中下载并提取的脚本所在的位置。
3. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
4. 运行 **Create-ADFSIdentityApp.ps1** 脚本。
5. 在“凭据”窗口中，输入 AD FS 云管理帐户和密码。 选择“确定”。
6. 提供[前面创建的证书](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)的证书文件路径和证书密码。 默认情况下值，为此步骤创建的证书是 **sso.appservice.local.azurestack.external.pfx**。

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 需要 | Null | Azure 资源管理器管理终结点。 例如 adminmanagement.local.azurestack.external。 |
| PrivilegedEndpoint | 需要 | Null | 特权终结点。 例如 AzS-ERCS01。 |
| CloudAdminCredential | 需要 | Null | Azure Stack 云管理的域帐户凭据。 例如 Azurestack\CloudAdmin。 |
| CertificateFilePath | 需要 | Null | 标识应用程序的证书 PFX 文件的**完整路径**。 |
| CertificatePassword | 需要 | Null | 帮助保护证书私钥的密码。 |

## <a name="next-steps"></a>后续步骤

[安装应用服务资源提供程序](azure-stack-app-service-deploy.md)

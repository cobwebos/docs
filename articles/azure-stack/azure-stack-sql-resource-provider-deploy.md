---
title: 在 Azure Stack 中使用 SQL 数据库 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署 SQL 数据库即服务，并通过便捷的步骤部署 SQL Server 资源提供程序适配器。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b06f53b0169e3afd140be81d9d633844a5876c09
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38487641"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>部署 Azure Stack 上的 SQL Server 资源提供程序

可以使用 Azure Stack SQL Server 资源提供程序来将 SQL 数据库公开为 Azure Stack 服务。 SQL 资源提供程序以服务的形式在 Windows Server 2016 Server Core 虚拟机 (VM) 上运行。

## <a name="prerequisites"></a>必备组件

需要先实施几个先决条件，然后才能部署 Azure Stack SQL 资源提供程序。 若要满足这些要求，请在可访问特权终结点 VM 的计算机上完成以下步骤：

- 向 Azure [注册 Azure Stack](.\azure-stack-registration.md)（如果尚未执行此操作），以便可以下载 Azure 市场项。
- 必须安装的 Azure 和 Azure Stack PowerShell 模块上系统是否将运行此安装。 该系统必须是.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 映像。 请参阅[安装适用于 Azure Stack PowerShell](.\azure-stack-powershell-install.md)。
- 下载 **Windows Server 2016 Datacenter - Server Core** 映像，将所需的 Windows Server 核心 VM 添加到 Azure Stack 市场。 

  >[!NOTE]
  >如果你需要安装更新，您可以在本地依赖项路径中放置一个 MSU 包。 如果找到多个 MSU 文件，则 SQL 资源提供程序安装将失败。

- 下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请务必下载适用于运行中 Azure Stack 版本的正确二进制文件。

    |Azure Stack 版本|SQL RP 版本|
    |-----|-----|
    |版本 1804 (1.0.180513.1)|[SQL RP 版本 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |版本 1802 (1.0.180302.1)|[SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|

### <a name="certificates"></a>证书

_仅适用于集成的系统安装_。 必须提供 [Azure Stack 部署 PKI 要求](.\azure-stack-pki-certs.md#optional-paas-certificates)中的“可选 PaaS 证书”部分所述的 SQL PaaS PKI 证书。 将 .pfx 文件放在 **DependencyFilesLocalPath** 参数指定的位置。 对于 ASDK 系统不提供证书。

## <a name="deploy-the-sql-resource-provider"></a>部署 SQL 资源提供程序

安装所有必备组件后，请运行 **DeploySqlProvider.ps1** 脚本部署 SQL 资源提供程序。 DeploySqlProvider.ps1 脚本是从针对 Azure Stack 版本下载的 SQL 资源提供程序二进制文件中提取的。

若要部署 SQL 资源提供程序，请打开一个权限提升的 PowerShell 控制台**新**窗口，并切换到解压缩后的 SQL 资源提供程序二进制文件所在的目录。 我们建议使用新的 PowerShell 窗口，以避免已加载的 PowerShell 模块造成问题。

运行 DeploySqlProvider.ps1 脚本，以完成以下任务：

- 将证书和其他项目上传到 Azure Stack 上的存储帐户。
- 发布库包，以便可以使用库部署 SQL 数据库。
- 发布用于部署宿主服务器的库包。
- 使用下载的 Windows Server 2016 核心映像部署 VM，然后安装 SQL 资源提供程序。
- 注册映射到资源提供程序 VM 的本地 DNS 记录。
- 将资源提供程序注册到操作员和用户帐户的本地 Azure 资源管理器。
- （可选）在资源提供程序安装期间安装单个 Windows Server 更新。

> [!NOTE]
> 当 SQL 资源提供程序部署开始时，将创建 **system.local.sqladapter** 资源组。 可能需要最多 75 分钟才能完成到此资源组所需的部署。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数

可在命令行中指定以下参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 对于集成系统，证书.pfx 文件必须放置此目录中。 （可选） 可以复制一个 Windows 更新 MSU 包此处。 | _可选_（对于集成系统为强制的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>使用自定义脚本部署 SQL 资源提供程序

若要在部署资源提供程序时消除任何手动配置，可以自定义以下脚本。 更改 Azure Stack 部署所需的默认帐户信息和密码。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module  -Name AzureStack -RequiredVersion 1.3.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

资源提供程序安装脚本完成后，刷新浏览器，以确保能够看到最新的更新。

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署

可以使用以下步骤来验证是否已成功部署 SQL 资源提供程序。

1. 以服务管理员身份登录到管理门户。
2. 选择“资源组”。
3. 选择“system.\<位置\>.sqladapter”资源组。
4. 在资源组概述摘要页上，应为没有部署失败。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)

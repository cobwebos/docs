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
ms.date: 05/24/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 8643e75a24ff7840b71dfaceae9934cdda566d30
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604414"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库
可以使用 Azure Stack SQL Server 资源提供程序来将 SQL 数据库公开为 Azure Stack 的一项服务。 SQL 资源提供程序服务在 SQL 资源提供程序 VM 上运行，该 VM 是一台 Windows Server 核心虚拟机。

## <a name="prerequisites"></a>必备组件
需要先实施几个先决条件，然后才能部署 Azure Stack SQL 资源提供程序。 在可以访问特权终结点 VM 的计算机上执行以下步骤：

- 向 Azure [注册 Azure Stack](.\azure-stack-registration.md)（如果尚未执行此操作），以便可以下载 Azure Marketplace 项。
- 通过下载 **Windows Server 2016 服务器核心**映像将所需的 Windows Server 核心 VM 添加到 Azure Stack Marketplace。 如需安装更新，可以将单个 .MSU 包放置在本地依赖项路径中。 如果发现了多个 .MSU 文件，则 SQL 资源提供程序安装将失败。
- 下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请务必下载适用于运行中 Azure Stack 版本的正确二进制文件：

    |Azure 堆栈版本|SQL RP 版本|
    |-----|-----|
    |版本 1804 (1.0.180513.1)|[SQL RP 版本 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |版本 1802 (1.0.180302.1)|[SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |版本 1712 （1.0.180102.3、 1.0.180103.2 或 1.0.180106.1 （集成系统））|[SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |
- 对于集成系统安装（仅限此类安装），必须通过在 **DependencyFilesLocalPath** 参数指定的位置中放置 .pfx 文件来提供 SQL PaaS PKI 证书，如 [Azure Stack 部署 PKI 要求](.\azure-stack-pki-certs.md#optional-paas-certificates)的“可选 PaaS 证书”部分中所述。

## <a name="deploy-the-sql-resource-provider"></a>部署 SQL 资源提供程序
在通过满足所有先决条件来成功执行 SQL 资源提供程序安装准备工作后，可以运行 **DeploySqlProvider.ps1** 脚本来部署 SQL 资源提供程序。 DeploySqlProvider.ps1 脚本是作为你根据 Azure Stack 版本下载的 SQL 资源提供程序二进制文件的一部分提取的。 

> [!IMPORTANT]
> 运行脚本的系统必须是装有最新版 .NET 运行时的 Windows 10 或 Windows Server 2016 系统。


若要部署 SQL 资源提供程序，请打开一个新的权限提升的（管理）PowerShell 控制台，并切换到解压缩后的 SQL 资源提供程序二进制文件所在的目录。

> [!NOTE]
> 使用新的 PowerShell 控制台窗口可以避免因系统上已加载的错误 PowerShell 模块可能造成的问题。

运行 DeploySqlProvider.ps1 脚本，它执行以下步骤：
- 将证书和其他项目上传到 Azure Stack 上的存储帐户。
- 发布库包，以便通过库部署 SQL 数据库。
- 发布用于部署宿主服务器的库包。
- 使用步骤 1 中创建的 Windows Server 2016 映像部署 VM，然后安装资源提供程序。
- 注册映射到资源提供程序 VM 的本地 DNS 记录。
- 将资源提供程序注册到本地 Azure 资源管理器（用户和管理员）。
- （可选）在 RP 安装期间安装单个 Windows 更新。

SQL 资源提供程序部署将开始，并创建 system.local.sqladapter 资源组。 可能需要花费多达 75 分钟才能完成此资源组的四个必需部署。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于集成系统为强制的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

>[!NOTE]
> SKU 最长可能需要在一小时后才显示在门户中。 在创建 SKU 之前，无法创建数据库。


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>使用自定义脚本部署 SQL 资源提供程序
若要避免在 DeploySqlProvider.ps1 脚本运行时手动输入所需信息，可以通过根据需要更改默认的帐户信息和密码来自定义以下脚本示例：

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
$tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署
可以使用本部分中的步骤来确保 SQL 资源提供程序已成功部署。

> [!NOTE]
>  在安装脚本完成运行之后，需要刷新门户才能看到管理边栏选项卡和库项。

1. 以服务管理员身份登录到管理门户。

2. 验证部署是否成功。 转到“资源组”。 然后选择“system.\<位置\>.sqladapter”资源组。 验证所有四个部署是否成功。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)

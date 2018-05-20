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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库
使用 Azure 堆栈 SQL Server 资源提供程序作为 Azure 堆栈的服务公开 SQL 数据库。 在 SQL 资源提供程序 VM，这是 Windows Server 内核的虚拟机上运行的 SQL 资源提供程序服务。

## <a name="prerequisites"></a>必备组件
有一些需要准备就绪可以部署 Azure 堆栈 SQL 资源提供程序之前的几个先决条件。 可以访问特权终结点 VM 的计算机上执行以下步骤：

- 如果你尚未这样做，请[注册 Azure 堆栈](.\azure-stack-registration.md)使用 Azure，以便你可以下载 Azure 应用商店项。
- 通过下载将在所需的 Windows Server core VM 添加到 Azure 堆栈 marketplace **Windows Server 2016 服务器核心**映像。 如需安装更新，可以将单个 .MSU 包放置在本地依赖项路径中。 如果多个。MSU 文件位于，SQL 资源提供程序安装将失败。
- 下载二进制的 SQL 资源提供程序，然后运行自解压缩程序中，若要将内容提取到临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请务必下载正确的二进制文件的正在运行的 Azure 堆栈的版本：
    - Azure 堆栈版本 1802 (1.0.180302.1): [SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802)。
    - Azure 堆栈版本 1712 （1.0.180102.3、 1.0.180103.2 或 1.0.180106.1 （集成系统））： [SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712)。
- 仅适用于集成的系统安装，你必须提供 SQL PaaS PKI 证书的可选 PaaS 证书部分中所述[Azure 堆栈部署 PKI 要求](.\azure-stack-pki-certs.md#optional-paas-certificates)，通过将.pfx 文件放入位置指定的**DependencyFilesLocalPath**参数。
- 确保你有[最新版本的 Azure 堆栈 PowerShell](.\azure-stack-powershell-install.md) (v1.2.11) 安装。 

## <a name="deploy-the-sql-resource-provider"></a>部署 SQL 资源提供程序
已成功准备好安装 SQL 资源提供程序满足所有先决条件后，你现在可以运行**DeploySqlProvider.ps1**脚本部署 SQL 资源提供程序。 下载对应于 Azure 堆栈版本作为二进制 SQL 资源提供程序的一部分，提取 DeploySqlProvider.ps1 脚本。 

> [!IMPORTANT]
> 正在运行该脚本的系统必须安装的.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 系统。


若要部署 SQL 资源提供程序，打开一个新的提升权限 （管理） 的 PowerShell 控制台，并将更改为你在哪里提取的 SQL 资源提供程序二进制文件的目录。

> [!NOTE]
> 使用新的 PowerShell 控制台窗口以避免可能不正确的已加载 PowerShell 模块从系统出现的问题。

运行 DeploySqlProvider.ps1 脚本，将执行以下步骤：
- 将证书和其他项目上传到 Azure Stack 上的存储帐户。
- 发布库包，以便通过库部署 SQL 数据库。
- 发布用于部署宿主服务器的库包。
- 使用步骤 1 中创建的 Windows Server 2016 映像部署 VM，然后安装资源提供程序。
- 注册映射到资源提供程序 VM 的本地 DNS 记录。
- 将资源提供程序注册到本地 Azure 资源管理器（用户和管理员）。
- （可选） 在 RP 安装期间安装单个 Windows 更新。

SQL 资源提供程序部署开始，并创建 system.local.sqladapter 资源组。 可能需要最多 75 分钟的时间才能完成到此资源组四个所需的部署。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_(_必需_为集成的系统) |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

>[!NOTE]
> SKU 最长可能需要在一小时后才显示在门户中。 在创建 SKU 之前，无法创建数据库。


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>部署 SQL 资源提供程序使用自定义脚本
若要避免 DeploySqlProvider.ps1 脚本运行时手动输入所需的信息，可以通过更改的默认帐户信息和密码，根据需要自定义以下脚本示例：

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署
可以使用此部分中的步骤，以确保 SQL 资源提供程序已成功部署。

> [!NOTE]
>  安装脚本完成运行后，你需要刷新门户后，以查看管理边栏选项卡和库项。

1. 以服务管理员身份登录到管理门户。

2. 验证部署是否成功。 转到“资源组”。 然后选择“system.\<位置\>.sqladapter”资源组。 验证所有四个部署是否成功。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>后续步骤

[将宿主服务器添加](azure-stack-sql-resource-provider-hosting-servers.md)

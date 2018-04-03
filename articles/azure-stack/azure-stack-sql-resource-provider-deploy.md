---
title: 在 Azure Stack 中使用 SQL 数据库 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署 SQL 数据库即服务，并通过便捷的步骤部署 SQL Server 资源提供程序适配器。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: d0b287eb61087e90c898aad5273ab5be8c1f98b2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 SQL Server 资源提供程序适配器可以公开 [Azure Stack](azure-stack-poc.md) 的 SQL 数据库即服务。 安装资源提供程序并将它连接到一个或多个 SQL Server 实例之后，你和用户可以创建：
- 适用于云原生应用的数据库。
- 基于 SQL 的网站。
- 基于 SQL 的工作负荷。
无需每次预配托管 SQL Server 的虚拟机 (VM)。

资源提供程序并不支持 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)的所有数据库管理功能。 例如，不会提供弹性数据库池以及自动提升和降低数据库性能的功能。 但是，资源提供程序支持类似的创建、读取、更新和删除 (CRUD) 操作。 API 与 SQL 数据库不兼容。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构
资源提供程序由三个组件构成：

- **SQL 资源提供程序适配器 VM**，即运行提供程序服务的 Windows 虚拟机。
- **资源提供程序本身**，处理预配请求并公开数据库资源。
- **托管 SQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。

你必须创建一个 （或多个） 的 SQL Server 的实例和/或提供对外部的 SQL Server 实例的访问。

> [!NOTE]
> 宿主在 Azure 堆栈安装的服务器必须从租户订阅创建集成的系统。 而不能通过默认提供商订阅创建。 必须通过租户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 VM，并且必须具有相应的许可证。 服务管理员可以是租户订阅的所有者。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，请注册开发工具包，并通过 Marketplace 管理下载 Windows Server 2016 Datacenter Core 映像。 必须使用 Windows Server 2016 Core 映像。 也可以使用脚本创建 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择“Core”选项）。

2. 登录到可访问特权终结点 VM 的主机。

    - 在 Azure Stack 开发工具包安装中，登录到物理主机。

    - 在多节点系统上，该主机必须是可访问特权终结点的系统。
    
    >[!NOTE]
    > 运行脚本的系统必须是装有最新版 .NET 运行时的 Windows 10 或 Windows Server 2016 系统。 否则安装会失败。 Azure Stack SDK 主机满足此条件。


3. 下载 SQL 资源提供程序二进制文件。 然后运行自解压程序，将内容解压缩到临时目录。

    >[!NOTE] 
    > 资源提供程序具有最小相应 Azure 堆栈生成。 请务必下载适用于运行中 Azure Stack 版本的正确二进制文件。

    | Azure Stack 内部版本 | SQL 资源提供程序安装程序 |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3、 1.0.180103.2 或 1.0.180106.1 （多节点） | [SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP 版本 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP 版本 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. 对于 Azure Stack SDK，将在此过程中创建自签名证书。 对于集成系统，你必须提供适当的证书。

   若要提供自己的证书，请将 .pfx 文件放在 **DependencyFilesLocalPath** 中，如下所示：

    - \*.dbadapter.\<区域\>.\<外部 FQDN\> 的通配符证书，或采用 sqladapter.dbadapter.\<区域\>.\<外部 FQDN\> 公用名的单个站点证书。

    - 此证书必须受信任。 也就是说，信任链必须存在，且不需要中间证书。

    - 只有一个单个证书文件可以存在于由 DependencyFilesLocalPath 参数指向的目录。

    - 文件名不能包含任何特殊字符。


5. 打开权限提升的（管理）**新** PowerShell 控制台，并切换到解压缩后的文件所在的目录。 使用新窗口可以避免因系统上已加载的错误 PowerShell 模块可能造成的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 DeploySqlProvider.ps1 脚本以执行以下步骤：

    - 将证书和其他项目上传到 Azure Stack 上的存储帐户。
    - 发布库包，以便通过库部署 SQL 数据库。
    - 发布用于部署宿主服务器的库包。
    - 使用步骤 1 中创建的 Windows Server 2016 映像部署 VM，然后安装资源提供程序。
    - 注册映射到资源提供程序 VM 的本地 DNS 记录。
    - 将资源提供程序注册到本地 Azure 资源管理器（用户和管理员）。
    - （可选） 在 RP 安装过程中安装的单个 Windows 更新

8. 我们建议你从应用商店管理下载最新的 Windows Server 2016 Core 映像。 如果你需要安装更新，你可以将单个。MSU 本地依赖项路径中的包。 如果多个。MSU 文件找不到，则脚本将失败。


下面是可从 PowerShell 命令提示符运行的示例命令。 （请务必根据需要更改帐户信息和密码。）

```
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

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于多节点部署是_必需_的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署

> [!NOTE]
>  在安装脚本完成运行之后，必须刷新门户才能看到管理边栏选项卡。


1. 以服务管理员身份登录到管理门户。

2. 验证部署是否成功。 转到“资源组”。 然后选择“system.\<位置\>.sqladapter”资源组。 验证所有四个部署是否成功。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 SQL 资源提供程序适配器（仅限多节点，1710 和更高版本）
更新 Azure 堆栈生成时，可能会发布新的 SQL 资源提供程序适配器。 虽然现有适配器继续工作，我们建议尽快更新到最新版本。 必须按顺序安装更新： 不能跳过版本 (请参阅表中的第 3 步[部署资源提供程序](#deploy-the-resource-provider))。

你使用的资源提供程序的更新*UpdateSQLProvider.ps1*脚本。 此过程是到用于安装资源提供程序，如中所述的过程类似[部署资源提供程序](#deploy-the-resource-provider)部分中的所述。 该脚本下载所随附的资源提供程序。

*UpdateSQLProvider.ps1*脚本使用最新的资源提供程序代码创建新的 VM，并将设置从旧的虚拟机迁移到新的 VM。 迁移的设置包括数据库和托管服务器信息，并需要 DNS 记录。

该脚本 DeploySqlProvider.ps1 脚本需要使用所述的相同自变量。 请同样在此处提供证书。 

我们建议你从应用商店管理下载最新的 Windows Server 2016 Core 映像。 如果你需要安装更新，你可以将单个。MSU 本地依赖项路径中的包。 如果多个。MSU 文件找不到，则脚本将失败。

以下是一种*UpdateSQLProvider.ps1*可以从 PowerShell 提示符下运行的脚本。 请务必根据需要更改帐户信息和密码： 

> [!NOTE]
> 在更新过程仅适用于集成的系统。

```
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

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于多节点部署是_必需_的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | （必需） |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** |每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |


## <a name="collect-diagnostic-logs"></a>收集诊断日志
SQL 资源提供程序是锁定停止虚拟机。 如果有必要对从虚拟机，PowerShell Just Enough Administration (JEA) 终结点收集日志_DBAdapterDiagnostics_为该目的提供。 有两个命令可通过此终结点：

* Get AzsDBAdapterLog-准备包含 RP 诊断日志的 zip 包，并将其放在会话用户驱动器上。 该命令可以不带任何参数调用，并将收集最后一个四个小时的日志。
* 删除-AzsDBAdapterLog-清理资源提供程序 VM 上的现有日志包

用户帐户调用_dbadapterdiag_ RP 部署或更新用于连接到用于提取 RP 日志的诊断终结点期间创建。 此帐户的密码是部署/更新的过程中提供的本地管理员帐户的密码相同。

若要使用这些命令，将需要创建与资源提供程序虚拟机的远程 PowerShell 会话和调用该命令。 你可以根据需要提供 FromDate 和 ToDate 参数。 如果你不指定一个或这两种，FromDate 将为当前时间前四个小时，ToDate 将当前时间。

此示例脚本演示如何使用这些命令：

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>维护操作 （集成系统）
SQL 资源提供程序是锁定停止虚拟机。 更新资源提供程序虚拟机的安全可以通过 PowerShell Just Enough Administration (JEA) 终结点_DBAdapterMaintenance_。

使用 RP 的安装包，以便这些操作提供了一个脚本。

### <a name="update-the-virtual-machine-operating-system"></a>更新虚拟机操作系统
有几种方法更新 Windows Server VM:
* 安装最新的资源提供程序包以使用当前修补的 Windows Server 2016 Core 映像
* 在安装或更新的 RP 的过程中安装 Windows 更新包


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虚拟机 Windows Defender 定义

请按照下列步骤来更新 Defender 定义：

1. Windows Defender 定义更新从下载[Windows Defender 定义](https://www.microsoft.com/en-us/wdsi/definitions)

    在该页上，在"手动下载和安装定义"下载"Windows Defender 防病毒软件的 Windows 10 和 Windows 8.1"64 位文件。 
    
    直接链接： https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. 创建与 SQL RP 适配器虚拟机维护终结点的 PowerShell 会话
3. 将定义更新文件复制到使用维护终结点会话 DB 适配器计算机
4. 维护 PowerShell 会话调用_更新 DBAdapterWindowsDefenderDefinitions_命令
5. 安装后，建议删除用于的定义更新文件。 在上维护会话使用，则可以移除_删除 ItemOnUserDrive)_命令。


下面是示例脚本来更新 （替换的地址或实际值的虚拟机的名称） 的 Defender 定义：

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>删除 SQL 资源提供程序适配器

若要删除资源提供程序，必须先删除所有依赖项。

1. 确保已保留针对此 SQL 资源提供程序适配器版本下载的原始部署包。

2. 必须从资源提供程序中删除所有用户数据库。 （删除用户数据库不会删除数据。）此任务应由用户自己执行。

3. 管理员必须从 SQL 资源提供程序适配器中删除宿主服务器。

4. 管理员必须删除引用 SQL 资源提供程序适配器的所有计划。

5. 管理员必须删除与 SQL 资源提供程序适配器关联的所有 SKU 和配额。

6. 使用以下元素重新运行部署脚本：
    - -Uninstall 参数
    - Azure 资源管理器终结点
    - DirectoryTenantID
    - 服务管理员帐户的凭据


## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)和[创建数据库](azure-stack-sql-resource-provider-databases.md)。

试用其他 [PaaS 服务](azure-stack-tools-paas-services.md)，例如 [MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)和[应用服务资源提供程序](azure-stack-app-service-overview.md)。

---
title: "使用 Azure 堆栈上的 SQL 数据库 |Microsoft 文档"
description: "了解如何部署 SQL 数据库作为 Azure 堆栈和快速步骤来部署 SQL Server 资源提供程序适配器上的服务。"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 4d2a00f04e5b07aeb3585fb3ab6c8966e0de7e19
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 SQL Server 资源提供程序适配器来公开 SQL 数据库作为一项服务的[Azure 堆栈](azure-stack-poc.md)。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，可以创建你和你的用户：
- 对于云本机应用程序的数据库。
- 基于 SQL 的网站。
- 基于 SQL 的工作负荷。
你无需设置虚拟机 (VM) 在每次承载 SQL Server。

资源提供程序不支持的所有数据库管理功能[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)。 例如，弹性数据库池和自动向上和向下拨号数据库性能的功能将不可用。 但是，类似的资源提供程序执行支持创建、 读取、 更新和删除 (CRUD) 操作。 API 不与 SQL 数据库兼容。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构
资源提供程序包括三个组件：

- **SQL 资源提供程序适配器 VM**，这是运行的提供程序服务的 Windows 虚拟机。
- **资源提供程序本身**、 哪些流程预配请求和公开数据库资源。
- **承载 SQL Server 的服务器**，它提供的信息的数据库的容量调用宿主服务器。

你必须创建一个 （或多个） 的 SQL Server 的实例和/或提供对外部的 SQL Server 实例的访问。

> [!NOTE]
> 宿主在 Azure 堆栈安装的服务器必须从租户订阅创建集成的系统。 无法从默认提供程序订阅中创建它们。 从租户门户或使用相应登录的 PowerShell 会话，必须创建它们。 所有宿主服务器是应计费 Vm，并且必须具有合适的许可证。 服务管理员可以是租户订阅的所有者。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，注册你开发工具包，并下载可下载通过应用商店管理 Windows Server 2016 数据中心 Core 映像。 你必须使用 Windows Server 2016 Core 映像。 你还可以使用脚本创建[Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择核心选项。）

2. 登录到可以访问特权终结点 VM 的主机。

    - 在 Azure 堆栈开发工具包安装中，登录到物理主机。

    - 在多节点系统中，主机必须是一个系统，可以访问特权终结点。
    
    >[!NOTE]
    > 正在运行该脚本的系统*必须*要安装的.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 系统。 安装否则将失败。 Azure 堆栈 SDK 主机都能满足此条件。


3. 下载二进制 SQL 资源提供程序。 然后运行自解压缩程序中，若要将内容提取到临时目录。

    >[!NOTE] 
    > 资源提供程序具有最小相应 Azure 堆栈生成。 请务必下载正确版本的正在运行的 Azure 堆栈的二进制文件。

    | Azure 堆栈生成 | SQL 资源提供程序安装程序 |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3、 1.0.180103.2 或 1.0.180106.1 （多节点） | [SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP 版本 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP 版本 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. 从特权终结点中检索 Azure 堆栈根证书。 为 Azure 堆栈 SDK 中,，将创建一个自签名的证书作为此过程的一部分。 对于集成系统，你必须提供适当的证书。

   若要提供自己的证书，放置在一个.pfx 文件**DependencyFilesLocalPath** ，如下所示：

    - 通配符证书\*.dbadapter。\<区域\>。\<外部 fqdn\>或使用 sqladapter.dbadapter 的公用名的单个站点证书。\<区域\>。\<外部 fqdn\>。

    - 此证书必须受信任。 即，信任链必须存在而无需中间证书。

    - 只有一个单个证书文件可以存在于由 DependencyFilesLocalPath 参数指向的目录。

    - 文件名称必须不包含任何特殊字符。


5. 打开**新**提升 （管理） 的 PowerShell 控制台和对您将文件解压缩 directory 的更改。 使用新窗口以避免可能不正确的已加载 PowerShell 模块从系统出现的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 DeploySqlProvider.ps1 脚本，执行以下步骤：

    - 将证书和其他项目上载到 Azure 堆栈上的存储帐户。
    - 将发布库包，以便你可以部署通过库的 SQL 数据库。
    - 发布库包部署托管的服务器。
    - 使用在步骤 1 中创建，然后安装资源提供程序的 Windows Server 2016 映像部署 VM。
    - 注册映射到你的资源提供程序 VM 的本地 DNS 记录。
    - 注册资源提供程序与本地 Azure 资源管理器 （用户和管理员）。
    - （可选） 在 RP 安装过程中安装的单个 Windows 更新

8. 我们建议你从应用商店管理下载最新的 Windows Server 2016 Core 映像。 如果你需要安装更新，你可以将单个。MSU 本地依赖项路径中的包。 如果多个。MSU 文件找不到，则脚本将失败。


下面是可以从 PowerShell 提示符下运行的一个示例。 （请务必更改的帐户信息和根据需要的密码。）

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
你可以在命令行中指定这些参数。 如果你不希望这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 因为你用于部署 Azure 堆栈，请使用相同的凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 必须在此目录中放置您的证书.pfx 文件。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 中删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>验证使用 Azure 堆栈门户部署

> [!NOTE]
>  安装脚本完成运行后，你需要刷新门户后，以查看管理边栏选项卡。


1. 服务管理员身份登录到管理门户。

2. 验证部署成功完成。 转到**资源组**。 然后选择**系统。\<位置\>.sqladapter**资源组。 验证所有四个部署成功完成。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 SQL 资源提供程序适配器 （多节点仅，生成 1710年及更高版本）
更新 Azure 堆栈生成时，可能会发布新的 SQL 资源提供程序适配器。 虽然现有适配器继续工作，我们建议尽快更新到最新版本。 必须按顺序安装更新： 不能跳过版本 (请参阅表中的第 3 步[部署资源提供程序](#deploy-the-resource-provider))。

你使用的资源提供程序的更新*UpdateSQLProvider.ps1*脚本。 此过程是到用于安装资源提供程序，如中所述的过程类似[部署资源提供程序](#deploy-the-resource-provider)部分中的所述。 该脚本下载所随附的资源提供程序。

*UpdateSQLProvider.ps1*脚本使用最新的资源提供程序代码创建新的 VM，并将设置从旧的虚拟机迁移到新的 VM。 迁移的设置包括数据库和托管服务器信息，并需要 DNS 记录。

该脚本 DeploySqlProvider.ps1 脚本需要使用所述的相同自变量。 也提供此处的证书。 

我们建议你从应用商店管理下载最新的 Windows Server 2016 Core 映像。 如果你需要安装更新，你可以将单个。MSU 本地依赖项路径中的包。 如果多个。MSU 文件找不到，则脚本将失败。

以下是一种*UpdateSQLProvider.ps1*可以从 PowerShell 提示符下运行的脚本。 请务必更改的帐户信息和根据需要的密码： 

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

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
你可以在命令行中指定这些参数。 如果你不希望这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 使用相同的凭据用于部署 Azure 堆栈。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 必须在此目录中放置您的证书.pfx 文件。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | （必需） |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** |重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 中删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |


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

若要删除资源提供程序，请务必首先删除任何依赖关系。

1. 确保你有你下载的此版本的 SQL 资源提供程序适配器的原始部署包。

2. 从资源提供程序，必须删除所有用户数据库。 （删除用户数据库不会删除数据。）应由用户自己才能执行此任务。

3. 管理员必须从 SQL 资源提供程序适配器中删除宿主服务器。

4. 管理员必须删除任何引用 SQL 资源提供程序适配器的计划。

5. 管理员必须删除任何 Sku 和与 SQL 资源提供程序适配器关联的配额。

6. 重新运行部署脚本替换为以下元素：
    - -卸载参数
    - Azure 资源管理器终结点
    - The DirectoryTenantID
    - 服务管理员帐户的凭据


## <a name="next-steps"></a>后续步骤

[将宿主服务器添加](azure-stack-sql-resource-provider-hosting-servers.md)和[创建数据库](azure-stack-sql-resource-provider-databases.md)。

尝试使用其他[PaaS 服务](azure-stack-tools-paas-services.md)如[MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)和[应用程序服务资源提供程序](azure-stack-app-service-overview.md)。

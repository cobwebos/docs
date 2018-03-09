---
title: "MySQL 数据库用作 Azure 堆栈上的 PaaS |Microsoft 文档"
description: "了解如何部署 MySQL 资源提供程序和提供作为 Azure 堆栈上的服务的 MySQL 数据库。"
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
ms.date: 03/06/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 067e478548ba840ece14737cdf3e6d5d4da28be0
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 MySQL 数据库

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以部署 Azure 堆栈上的 MySQL 资源提供程序。 在部署资源提供程序后，你可以创建 MySQL 服务器和数据库通过 Azure 资源管理器部署模板。 你还可以作为一种服务提供 MySQL 数据库。 

MySQL 数据库，共有网站，支持多个网站平台。 例如，在部署资源提供程序后，你可以创建 WordPress 网站从 Web 应用程序平台作为服务 (PaaS) 外接程序用于 Azure 堆栈。

若要部署不能访问 Internet 的系统上的 MySQL 提供程序，请将文件复制[mysql 连接器 net 6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi)到本地共享。 当提示你输入它，然后提供该共享名称。 你必须安装的 Azure 和 Azure 堆栈 PowerShell 模块。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server 资源提供程序适配器体系结构

资源提供程序由三个组件组成：

- **MySQL 资源提供程序适配器 VM**，即 Windows 虚拟机运行的提供程序服务。

- **资源提供程序本身**、 哪些流程预配请求和公开数据库资源。

- **承载 MySQL Server 的服务器**，它提供的信息调用宿主服务器的数据库的容量。

此版本不再创建 MySQL 实例。 这意味着你需要自行创建和/或提供对外部的 SQL 实例的访问。 请访问[Azure 堆栈快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)有关示例模板，可以：
- 为你创建的 MySQL 服务器。
- 下载并部署从 Azure 应用商店创建 MySQL 服务器。

> [!NOTE]
> 宿主在 Azure 堆栈安装的服务器必须从租户订阅创建集成的系统。 无法从默认提供程序订阅中创建它们。 从租户门户或使用相应登录的 PowerShell 会话，必须创建它们。 所有宿主服务器是应计费 Vm，并且必须具有合适的许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>必需的权限
系统帐户必须具有以下权限：

1.  数据库： 创建、 删除
2.  登录名： 创建、 设置、 删除、 授予、 撤消

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，注册你开发工具包，并下载可下载通过应用商店管理 Windows Server 2016 数据中心 Core 映像。 你必须使用 Windows Server 2016 Core 映像。 你还可以使用脚本创建[Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择核心选项。）


2. 登录到可以访问特权终结点 VM 的主机。

    - 在 Azure SDK 安装中，登录到物理主机。 
    - 在多节点系统中，主机必须是一个系统，可以访问特权终结点。
    
    >[!NOTE]
    > 系统在其运行该脚本*必须*要安装的.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 系统。 安装否则将失败。 Azure 堆栈 SDK 主机都能满足此条件。
    

3. 下载二进制 MySQL 资源提供程序。 然后运行自解压缩程序中，若要将内容提取到临时目录。

    >[!NOTE] 
    > 资源提供程序具有最小相应 Azure 堆栈生成。 请务必下载正确版本的正在运行的 Azure 堆栈的二进制文件。

    | Azure 堆栈生成 | MySQL RP 安装程序 |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 或 1.0.180106.1 （多节点） | [MySQL RP 版本 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP 版本 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP 版本 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  从特权终结点中检索 Azure 堆栈根证书。 针对 Azure SDK 中，将创建一个自签名的证书作为此过程的一部分。 对于多节点，你必须提供适当的证书。

    如果你需要提供你自己的证书，放置在一个.pfx 文件**DependencyFilesLocalPath**满足以下条件：

    - 通配符证书\*.dbadapter。\<区域\>。\<外部 fqdn\>或使用 mysqladapter.dbadapter 的公用名的单个站点证书。\<区域\>。\<外部 fqdn\>。

    - 此证书必须受信任。 即，信任链必须存在而无需中间证书。

    - 只有一个单个证书文件所在 DependencyFilesLocalPath。
    
    - 文件名称必须不包含任何特殊字符或空格。


5. 打开**新**提升 （管理） 的 PowerShell 控制台。 然后将更改为您将文件解压缩的目录。 使用新窗口以避免可能不正确的已加载 PowerShell 模块从系统出现的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 `DeployMySqlProvider.ps1` 脚本。

脚本执行以下步骤：

* 下载 MySQL 连接器二进制文件 （这可以提供脱机）。
* 将证书和其他项目上载到 Azure 堆栈上的存储帐户。
* 将发布库包，以便你可以部署通过库的 SQL 数据库。
* 发布库包部署托管的服务器。
* 使用步骤 1 中创建的 Windows Server 2016 映像部署 VM。 它还会安装资源提供程序。
* 注册映射到你的资源提供程序 VM 的本地 DNS 记录。
* 注册资源提供程序与本地 Azure 资源管理器 （租户和管理员）。


可以：
- 在命令行上指定所需的参数。
- 运行不带任何参数，然后输入系统提示时。

下面是可以从 PowerShell 提示符下运行的一个示例。 请务必更改的帐户信息和根据需要的密码：


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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
你可以在命令行中指定这些参数。 如果你不希望这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 使用相同的凭据用于部署 Azure 堆栈。 | _必需_ |
| **VMLocalCredential** | MySQL 资源提供程序 VM 的本地管理员帐户凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 包含本地共享路径[mysql 连接器 net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)。 如果你提供两个路径，该证书文件必须放置在此目录中。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 中删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |
| **AcceptLicense** | 跳过的提示时接受 GPL 许可证。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure 堆栈门户验证部署

> [!NOTE]
>  安装脚本完成运行后，你需要刷新门户后，以查看管理边栏选项卡。


1. 服务管理员身份登录到管理门户。

2. 验证部署成功完成。 转到**资源组**，然后选择**系统。\<位置\>.mysqladapter**资源组。 验证所有四个部署成功完成。

      ![验证 MySQL RP 的部署](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>提供通过连接到 MySQL 宿主服务器的容量

1. 登录到 Azure 堆栈门户为服务管理员。

2. 选择**管理资源** > **MySQL 宿主服务器** > **+ 添加**。

    上**MySQL 宿主服务器**边栏选项卡，可以将 MySQL Server 资源提供程序连接到实际的 MySQL Server 实例作为资源提供程序的后端。

    ![宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 提供您的 MySQL Server 实例的连接详细信息。 请确保提供的完全限定的域名 (FQDN) 或有效的 IPv4 地址，并不是短 VM 名称。 此安装不再提供的默认 MySQL 实例。 提供的大小可帮助管理数据库容量资源提供程序。 它应接近数据库服务器的物理容量。

    > [!NOTE]
    >可以通过租户和管理 Azure 资源管理器访问 MySQL 实例，可以放置在控制之下的资源提供程序。 MySQL 实例*必须*分配以独占方式与资源提供程序。

4. 添加服务器时，你必须为他们分配一种新的或现有的 SKU，以允许服务产品的不同之处。
  例如，你可以具有企业实例提供：
    - 数据库容量
    - 自动备份
    - 预留各部门的高性能服务器
 

SKU 名称应反映的属性，以便租户可以适当地将其数据库。 在 SKU 中的所有宿主服务器应具有相同的功能。

![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Sku 可以花费一个小时的时间无法在门户中显示。 无法创建一个数据库，直到创建 SKU。


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>通过创建你的第一个 MySQL 数据库来测试你的部署


1. 登录到 Azure 堆栈门户为服务管理员。

2. 选择**+ 新** > **数据 + 存储** > **MySQL 数据库**。

3. 提供数据库详细信息。

    ![创建测试 MySQL 数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 选择一种的 SKU。

    ![选择一种的 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 创建一个登录名设置。 你可以重用现有的登录名设置或创建一个新。 此设置包含的用户名称和数据库的密码。

    ![创建新的数据库登录名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    该连接字符串包含实际的数据库服务器名称。 从门户复制它。

    ![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> 用户名称的长度不能超过在 MySQL 5.7 32 个字符。 在早期版本中，它不能超过 16 个字符。


## <a name="add-capacity"></a>添加容量

通过在 Azure 堆栈门户中添加其他 MySQL 服务器添加容量。 其他服务器可以添加到一种新的或现有的 SKU。 请确保服务器特征都相同。


## <a name="make-mysql-databases-available-to-tenants"></a>使 MySQL 数据库可供租户使用
创建计划，并提供可使 MySQL 数据库供租户可用。 例如，添加 Microsoft.MySqlAdapter 服务、 添加配额，依次类推。

![创建计划，并提供要包括数据库](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>更新管理密码
可以通过第一个更改它的 MySQL server 实例上修改密码。 选择**管理资源** > **MySQL 宿主服务器**。 然后选择的宿主服务器。 在**设置**面板中，选择**密码**。

![更新管理员密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 MySQL 资源提供程序适配器 （多节点仅，生成 1710年及更高版本）
更新 Azure 堆栈生成时，可能会发布新的 SQL 资源提供程序适配器。 虽然现有适配器继续工作，我们建议尽快更新到最新版本。 

更新过程是类似于前面所述的安装过程。 使用最新的资源提供程序代码中创建新的 VM。 然后你将设置迁移到此新实例，包括数据库和托管服务器信息。 您也将迁移的必要的 DNS 记录。

UpdateMySQLProvider.ps1 脚本使用前面所述的相同自变量。 也提供此处的证书。

> [!NOTE]
> 在更新过程仅适用于集成的系统。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 parameters
你可以在命令行中指定这些参数。 如果不这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 因为你用于部署 Azure 堆栈，请使用相同的凭据。 | _必需_ |
| **VMLocalCredential** |SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 必须在此目录中放置您的证书.pfx 文件。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |
| **AcceptLicense** | 跳过的提示时接受 GPL 许可证。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>收集诊断日志
MySQL 资源提供程序是锁定停止虚拟机。 如果有必要对从虚拟机，PowerShell Just Enough Administration (JEA) 终结点收集日志_DBAdapterDiagnostics_为该目的提供。 有两个命令可通过此终结点：

* Get AzsDBAdapterLog-准备包含 RP 诊断日志的 zip 包，并将其放在会话用户驱动器上。 该命令可以不带任何参数调用，并将收集最后一个四个小时的日志。
* 删除-AzsDBAdapterLog-清理资源提供程序 VM 上的现有日志包

用户帐户调用_dbadapterdiag_ RP 部署或更新用于连接到用于提取 RP 日志的诊断终结点期间创建。 此帐户的密码是部署/更新的过程中提供的本地管理员帐户的密码相同。

若要使用这些命令，你需要创建与资源提供程序虚拟机的远程 PowerShell 会话和调用该命令。 你可以根据需要提供 FromDate 和 ToDate 参数。 如果你不指定一个或这两种，FromDate 将为当前时间前四个小时，ToDate 将当前时间。

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
MySQL 资源提供程序是锁定停止虚拟机。 更新资源提供程序虚拟机的安全可以通过 PowerShell Just Enough Administration (JEA) 终结点_DBAdapterMaintenance_。

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

2. 创建 MySQL RP 适配器虚拟机维护终结点的 PowerShell 会话
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>删除 MySQL 资源提供程序适配器

若要删除资源提供程序，请务必首先删除任何依赖关系。

1. 确保你有你下载的此版本的资源提供程序的原始部署包。

2. 从资源提供程序，必须删除所有租户数据库。 （删除租户数据库不会删除数据。）通过租户本身，应执行此任务。

3. 从命名空间中，租户必须取消注册。

4. 管理员必须从 MySQL 适配器中删除宿主服务器。

5. 管理员必须删除任何引用 MySQL 适配器的计划。

6. 管理员必须删除任何与 MySQL 适配器关联的配额。

7. 重新运行部署脚本替换为以下元素：
    - -卸载参数
    - Azure 资源管理器终结点
    - The DirectoryTenantID
    - 服务管理员帐户的凭据
